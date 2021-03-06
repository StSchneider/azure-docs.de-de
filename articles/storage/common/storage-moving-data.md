---
title: Verschieben großer Datenmengen in/aus Cloudspeicher in Azure | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über die verschiedenen Methoden zum Verschieben von Daten in und aus Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/26/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 76da33a74ad95d7f074bc4efd3a8d9f97c19d612
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830311"
---
# <a name="moving-data-to-and-from-azure-storage"></a>Verschieben von Daten in und aus Azure Storage
Wenn Sie lokale Daten in Azure Storage verschieben möchten (oder umgekehrt), steht Ihnen eine Vielzahl von Methoden zur Verfügung. Es hängt vom jeweiligen Szenario ab, welche Methode für Sie am besten geeignet ist. Dieser Artikel bietet eine kurze Übersicht über die verschiedenen Szenarien und die jeweiligen Angebote.

## <a name="building-applications"></a>Erstellen von Anwendungen
Wenn Sie eine Anwendung erstellen, ist das Entwickeln mit der REST-API oder einer unserer vielen Clientbibliotheken eine hervorragende Möglichkeit zum Verschieben von Daten in und aus Azure Storage.

Azure Storage bietet umfassende Clientbibliotheken für viele beliebte Sprachen, einschließlich .NET, Java, Android, Go, Xamarin, C++, Node.JS, PHP, Ruby, Python und iOS. Die Clientbibliotheken bieten erweiterte Funktionen, beispielsweise Wiederholungslogik, Protokollierung und parallele Uploads. Die Entwicklung kann direkt mit der REST-API erfolgen. Diese API lässt sich mithilfe jeder Sprache aufrufen, die HTTP/HTTPS-Anforderungen verarbeitet.

Weitere Informationen finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../blobs/storage-dotnet-how-to-use-blobs.md) .

Darüber hinaus bieten wir auch die [Azure Storage Data Movement Library](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) an, eine Bibliothek, die für das Kopieren von Daten zu und aus Azure mit hoher Leistung konzipiert wurde. Die [Dokumentation](https://github.com/Azure/azure-storage-net-data-movement) zu unserer Data Movement Library bietet weitere Informationen. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Schnelles Anzeigen/Interagieren mit den Daten
Wenn Sie Ihre Azure Storage-Daten auf einfache Weise anzeigen und gleichzeitig die Möglichkeit haben möchten, Ihre Daten hoch- und herunterzuladen, sollten Sie einen Azure Storage-Explorer verwenden.

In der Liste der [Azure Storage-Explorer](../storage-explorers.md) finden Sie weitere Informationen.

## <a name="system-administration"></a>Systemadministration
Wenn Sie ein Befehlszeilenprogramm benötigen oder mit einem solchen Programm besser vertraut sind (z. B. Systemadministratoren), stehen Ihnen u. a. die folgenden Optionen zur Auswahl:

### <a name="azcopy"></a>AzCopy
AzCopy ist ein Befehlszeilenprogramm, mit dem sehr effizient Daten in und aus Azure Storage kopiert werden können. Sie können auch Daten innerhalb eines Speicherkontos oder zwischen Speicherkonten kopieren. AzCopy ist unter [Windows](storage-use-azcopy.md) und unter [Linux](storage-use-azcopy-linux.md) verfügbar.

Informationen zum Migrieren von lokalen Daten zu Azure Storage finden Sie unter [Tutorial: Migrieren von lokalen Daten in Cloudspeicher mit AzCopy](storage-use-azcopy-migrate-on-premises-data.md).

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell ist ein Modul, das Cmdlets für die Verwaltung von Diensten in Azure bereitstellt. Bei diesem Modul handelt es sich um eine aufgabenbasierte Befehlszeilenshell und Skriptsprache, die speziell für die Systemverwaltung entwickelt wurde.

Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell mit Azure Storage](storage-powershell-guide-full.md) .

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Die Azure-Befehlszeilenschnittstelle stellt eine Reihe von plattformübergreifenden Open Source-Befehlen für die Arbeit mit Azure-Diensten bereit. Die Azure-Befehlszeilenschnittstelle ist unter Windows, OS X und Linux verfügbar.

Weitere Informationen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure-Speicher](../storage-azure-cli.md) .

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Verschieben großer Datenmengen in einem langsamen Netzwerk
Eines der größten Probleme beim Verschieben großer Datenmengen ist die Zeit für die Übertragung. Wenn Sie Daten in bzw. aus Azure Storage übertragen möchten, ohne die Kosten für Netzwerke bedenken oder Code schreiben zu müssen, ist Azure Import/Export eine geeignete Lösung.

Weitere Informationen finden Sie unter [Azure Import/Export](../storage-import-export-service.md) .

## <a name="backing-up-your-data"></a>Sichern der Daten
Wenn Sie einfach Ihre Daten in Azure Storage sichern müssen, ist Azure Backup die beste Wahl. Dies ist eine leistungsfähige Lösung zum Sichern von lokalen Daten und Azure-VMs.

Weitere Informationen finden Sie unter [Azure Backup](../../backup/backup-introduction-to-azure-backup.md) .

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Zugreifen auf Ihre Daten lokal und über die Cloud
Wenn Sie eine Lösung für den Zugriff auf Ihre Daten lokal und über die Cloud benötigen, dann sollten Sie StorSimple, die hybride Cloudspeicherlösung von Azure, in Betracht ziehen. Diese Lösung besteht aus einem physischen StorSimple-Gerät, auf dem auf intelligente Weise häufig verwendete Daten auf SSDs, gelegentlich verwendete Daten auf HDDs und inaktive/Backup-/Archivdaten in Azure Storage gespeichert werden.

Weitere Informationen finden Sie unter [StorSimple](../../storsimple/storsimple-overview.md) .

## <a name="recovering-your-data"></a>Wiederherstellen der Daten
Wenn Sie über lokale Workloads und Anwendungen verfügen, benötigen Sie eine Lösung, die Geschäftskontinuität im Falle eines Notfalls ermöglicht. Azure Site Recovery steuert Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server. Replizierte Daten werden in Azure Storage gespeichert, sodass Sie kein sekundäres lokales Rechenzentrum benötigen.

Weitere Informationen finden Sie unter [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) .
### <a name="moving-data-faq"></a>Häufig gestellte Fragen zum Verschieben von Daten:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Kann ich VHDs ohne Kopiervorgang aus einer Region in eine andere migrieren?
Die einzige Möglichkeit zum Kopieren von VHDs von einer Region in eine andere ist das Kopieren der Daten zwischen Speicherkonten in den jeweiligen Regionen. Sie können AZCopy dafür verwenden. Weitere Informationen finden Sie unter „Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy“. Für sehr große Datenmengen können Sie auch Azure Import/Export einsetzen. Weitere Informationen finden Sie unter [Azure Import/Export](https://docs.microsoft.com/azure/storage/storage-import-export-service) .
