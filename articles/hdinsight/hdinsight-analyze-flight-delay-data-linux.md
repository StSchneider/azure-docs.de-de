---
title: 'Tutorial: Ausführen von ETL-Vorgängen (Extrahieren, Transformieren, Laden) unter Verwendung von Hive in HDInsight: Azure '
description: Hier erfahren Sie, wie Sie Daten aus einem unformatierten CSV-Dataset extrahieren, mithilfe von Hive in HDInsight transformieren und die transformierten Daten mithilfe von Apache Sqoop in eine Azure SQL-Datenbank laden.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: e5ee2f40526837fbe0251e1fdda6847db1c51288
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634353"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>Tutorial: Extrahieren, Transformieren und Laden von Daten mithilfe von Apache Hive in Azure HDInsight

In diesem Tutorial importieren Sie eine unformatierte CSV-Datendatei in einen HDInsight-Clusterspeicher und transformieren die Daten anschließend mithilfe von [Apache Hive](https://hive.apache.org/) in Azure HDInsight. Danach laden Sie die transformierten Daten mithilfe von [Apache Sqoop](https://sqoop.apache.org/) in eine Azure SQL-Datenbank. In diesem Artikel werden öffentlich verfügbare Flugdaten verwendet.

> [!IMPORTANT]  
> Die Schritte in diesem Dokument erfordern einen HDInsight-Cluster mit Linux. Linux ist das einzige Betriebssystem, das unter Azure HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Dieses Tutorial enthält die folgenden Aufgaben: 

> [!div class="checklist"]
> * Herunterladen der Beispielflugdaten
> * Hochladen von Daten in einen HDInsight-Cluster
> * Transformieren der Daten mithilfe von Hive
> * Erstellen einer Tabelle in Azure SQL-Datenbank
> * Exportieren von Daten in Azure SQL-Datenbank mithilfe von Sqoop


Die folgende Abbildung zeigt einen typischen ETL-Anwendungsfluss:

![ETL-Vorgang unter Verwendung von Apache Hive in Azure HDInsight](./media/hdinsight-analyze-flight-delay-data-linux/hdinsight-etl-architecture.png "ETL-Vorgang unter Verwendung von Apache Hive in Azure HDInsight")

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* **Linux-basierter Hadoop-Cluster in HDInsight**. Schritte zum Erstellen eines neuen Linux-basierten HDInsight-Clusters finden Sie unter [Schnellstart: Erste Schritte mit Apache Hadoop und Apache Hive in Azure HDInsight mit der Resource Manager-Vorlage](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Azure SQL-Datenbank**. Sie verwenden eine Azure SQL-Datenbank als Zieldatenspeicher. Wenn Sie keine SQL-Datenbank besitzen, finden Sie Informationen unter [Erstellen einer Azure SQL-Datenbank im Azure-Portal](../sql-database/sql-database-get-started.md).

* **Azure-Befehlszeilenschnittstelle**. Informationen zum Installiere der Azure CLI finden Sie bei Bedarf unter [Installieren von Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **SSH-Client**. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Herunterladen der Flugdaten

1. Rufen Sie die Website von [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA) auf.

2. Wählen Sie auf der Website die folgenden Werte aus:

   | NAME | Wert |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | Felder |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Entfernen Sie die Häkchen bei allen anderen Feldern. 

3. Wählen Sie **Herunterladen** aus. Sie erhalten eine ZIP-Datei mit den ausgewählten Datenfeldern.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Hochladen von Daten in einen HDInsight-Cluster

Daten können auf unterschiedliche Weise in den zugeordneten Speicher eines HDInsight-Clusters hochgeladen werden. In diesem Abschnitt verwenden Sie `scp` zum Hochladen der Daten. Informationen zu anderen Uploadmöglichkeiten für Daten finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md).

1. Öffnen Sie eine Eingabeaufforderung, und verwenden Sie den folgenden Befehl, um die ZIP-Datei in den Hauptknoten des HDInsight-Clusters hochzuladen:

    ```bash
    scp <FILENAME>.zip <SSH-USERNAME>@<CLUSTERNAME>-ssh.azurehdinsight.net:<FILENAME.zip>
    ```

    Ersetzen Sie *FILENAME* durch den Namen der ZIP-Datei. Ersetzen Sie *USERNAME* durch die SSH-Anmeldedaten für den HDInsight-Cluster. Ersetzen Sie *CLUSTERNAME* durch den Namen des HDInsight-Clusters.

   > [!NOTE]  
   > Wenn Sie für die Authentifizierung Ihrer SSH-Anmeldung ein Kennwort verwenden, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen öffentlichen Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden und den Pfad zum passenden privaten Schlüssel angeben. Beispiel: `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Stellen Sie nach Abschluss des Uploadvorgangs eine SSH-Verbindung mit dem Cluster her. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

3. Extrahieren Sie die ZIP-Datei mit folgendem Befehl:

    ```bash
    unzip FILENAME.zip
    ```

    Dieser Befehl extrahiert eine CSV-Datei, die ungefähr 60 MB groß ist.

4. Verwenden Sie die folgenden Befehle, um ein Verzeichnis im HDInsight-Speicher zu erstellen und dann die CSV-Datei in das Verzeichnis zu kopieren:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put <FILENAME>.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformieren von Daten mithilfe einer Hive-Abfrage

Ein Hive-Auftrag kann auf verschiedenste Arten in einem HDInsight-Cluster ausgeführt werden. In diesem Abschnitt verwenden Sie [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell), um einen Hive-Auftrag auszuführen. Informationen zu anderen Ausführungsmethoden für Hive-Aufträge finden Sie unter [Was sind Apache Hive und HiveQL in Azure HDInsight?](./hadoop/hdinsight-use-hive.md).

Im Rahmen des Hive-Auftrags importieren Sie die Daten aus der CSV-Datei in eine Hive-Tabelle namens **Delays**.

1. Verwenden Sie an der SSH-Eingabeaufforderung, die bereits für den HDInsight-Cluster vorhanden ist, den folgenden Befehl, um eine neue Datei namens **flightdelays.hql** zu erstellen und zu bearbeiten:

    ```bash
    nano flightdelays.hql
    ```

2. Verwenden Sie als Inhalt der Datei den folgenden Text:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. Drücken Sie zum Speichern der Datei die**ESC-TASTE**, und geben Sie anschließend `:x` ein.

3. Starten Sie Hive mit dem folgenden Befehl, und führen Sie die Datei **flightdelays.hql** aus:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Öffnen Sie nach der Ausführung des Skripts __flightdelays.hql__ mithilfe des folgenden Befehls eine interaktive Beeline-Sitzung:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Wenn Sie die `jdbc:hive2://localhost:10001/>`-Eingabeaufforderung erhalten, rufen Sie die Daten mit der folgenden Abfrage aus den importierten Flugverspätungsdaten ab:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Mit dieser Abfrage werden eine Liste von Orten, in denen Verspätungen infolge des Wetters auftraten, sowie die durchschnittliche Verspätung abgerufen. Die Liste wird anschließend in `/tutorials/flightdelays/output` gespeichert. Sqoop liest später die Daten an diesem Speicherort und exportiert sie in die Azure SQL-Datenbank.

6. Geben Sie zum Beenden von Beeline `!quit` an der Eingabeaufforderung ein.

## <a name="create-a-sql-database-table"></a>Erstellen einer SQL-Datenbanktabelle

In diesem Abschnitt wird davon ausgegangen, dass Sie bereits eine Azure SQL-Datenbank erstellt haben. Wenn Sie noch nicht über eine SQL-Datenbank verfügen, nutzen Sie die Informationen in [Erstellen einer Azure SQL-Datenbank im Azure-Portal](../sql-database/sql-database-get-started.md), um eine Datenbank zu erstellen.

Wenn Sie bereits über eine SQL-Datenbank verfügen, müssen Sie den Namen des Servers abrufen. Klicken Sie zum Ermitteln des Servernamens im [Azure-Portal](https://portal.azure.com) auf **SQL-Datenbanken**, und filtern Sie nach dem Namen der gewünschten Datenbank. Der Servername befindet sich in der Spalte **Servername**.

![Abrufen von Details für den Azure SQL-Server](./media/hdinsight-analyze-flight-delay-data-linux/get-azure-sql-server-details.png "Abrufen von Details für den Azure SQL-Server")

> [!NOTE]  
> Es gibt viele Möglichkeiten, eine Verbindung mit der SQL-Datenbank herzustellen und eine Tabelle zu erstellen. Die folgenden Schritte verwenden [FreeTDS](http://www.freetds.org/) aus dem HDInsight-Cluster.


1. Verwenden Sie zum Installieren von FreeTDS den folgenden Befehl über eine SSH-Verbindung mit dem Cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Sobald die Installation abgeschlossen ist, verwenden Sie den folgenden Befehl, um eine Verbindung mit dem SQL-Datenbank-Server herzustellen. Ersetzen Sie **serverName** durch den Namen des SQL-Datenbank-Servers. Ersetzen Sie **adminLogin** und **adminPassword** durch die Anmeldung für die SQL-Datenbank. Ersetzen Sie **databaseName** mit dem Datenbanknamen.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -p 1433 -D <databaseName>
    ```

    Geben Sie nach entsprechender Aufforderung das Kennwort für die SQL-Datenbank-Administratoranmeldung ein.

    Eine Ausgabe ähnlich folgendem Text wird angezeigt:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Geben Sie bei der Eingabeaufforderung `1>` folgende Zeilen ein:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Nach Eingabe der Anweisung `GO` werden die vorherigen Anweisungen ausgewertet. Diese Abfrage erstellt eine Tabelle mit dem Namen **delays** mit einem gruppierten Index.

    Stellen Sie mit der folgenden Abfrage sicher, dass die Tabelle erstellt wurde:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    Die Ausgabe sieht in etwa wie folgender Text aus:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

5. EINGABE `exit` at the `1>` ein.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Exportieren von Daten in SQL-Datenbank mithilfe von Apache Sqoop

In den vorherigen Abschnitten haben Sie die transformierten Daten unter `/tutorials/flightdelays/output` kopiert. In diesem Abschnitt verwenden Sie Sqoop, um die Daten aus „/tutorials/flightdelays/output“ in die Tabelle zu exportieren, die Sie in Azure SQL-Datenbank erstellt haben. 

1. Verwenden Sie den folgenden Befehl, um zu überprüfen, ob Sqoop Ihre SQL-Datenbank erreichen kann:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Dieser Befehl gibt eine Liste mit Datenbanken zurück, in der auch die Datenbank enthalten ist, in der Sie bereits die Tabelle „delays“ erstellt haben.

2. Verwenden Sie den folgenden Befehl, um Daten aus „hivesampletable“ in die Tabelle „delays“ zu exportieren:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop stellt eine Verbindung mit der Datenbank her, die die Tabelle „delays“ enthält, und exportiert Daten aus dem Verzeichnis `/tutorials/flightdelays/output` in die Tabelle „delays“.

3. Nach Abschluss des Sqoop-Befehls stellen Sie wie folgt über das Hilfsprogramm „tsql“ eine Verbindung mit der Datenbank her:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Überprüfen Sie mithilfe der folgenden Anweisungen, ob die Daten in die Tabelle „delays“ exportiert wurden:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Es sollte eine Liste der Tabellendaten angezeigt werden. Die Tabelle enthält den Namen der Stadt und die durchschnittliche Flugverspätung für diese Stadt. 

    Geben Sie `exit` ein, um das Dienstprogramm tsql zu beenden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Sie ETL-Datenvorgänge (Extrahieren, Transformieren, Laden) unter Verwendung eines Apache Hadoop-Clusters in HDInsight ausführen. Im nächsten Tutorial erfahren Sie, wie Sie bedarfsgesteuerte HDInsight Hadoop-Cluster unter Verwendung von Azure Data Factory erstellen.

> [!div class="nextstepaction"]
>[Erstellen bedarfsgesteuerter Apache Hadoop-Cluster in HDInsight mit Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md)

Weitere Informationen zum Arbeiten mit Daten in HDInsight finden Sie in den folgenden Artikeln:

* [Tutorial: Extrahieren, Transformieren und Laden von Daten mithilfe von Apache Hive in Azure HDInsight](../storage/data-lake-storage/tutorial-extract-transform-load-hive.md)
* [Verwenden von Apache Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Apache Pig mit HDInsight][hdinsight-use-pig]
* [Entwickeln von Java MapReduce-Programmen für Apache Hadoop in HDInsight][hdinsight-develop-mapreduce]
* [Entwickeln von Streaming-MapReduce-Programmen für HDInsight mit Python][hdinsight-develop-streaming]
* [Verwenden von Apache Oozie mit HDInsight][hdinsight-use-oozie]
* [Importieren und Exportieren von Daten zwischen Apache Hadoop unter HDInsight und einer SQL-Datenbank mithilfe von Apache Sqoop][hdinsight-use-sqoop]



[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/


[rita-website]: https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
