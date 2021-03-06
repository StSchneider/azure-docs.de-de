---
title: Unterstützung für Azure Table Storage in Azure Cosmos DB
description: Erfahren Sie, wie die Azure Cosmos DB-Tabellen-API und Azure Storage-Tabellen zusammen funktionieren.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 11/15/2017
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: b105cf6c220534927a16be83ca5db8801c88f6c0
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035592"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Entwickeln mit der Azure Cosmos DB-Tabellen-API und Azure Table Storage

Die Azure Cosmos DB-Tabellen-API und Azure Table Storage nutzen dasselbe Tabellendatenmodell und dieselben Erstellungs-, Lösch-, Update- und Abfragevorgänge über ihre SDKs. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Entwickeln mit der Azure Cosmos DB-Tabellen-API

Derzeit sind für die [Tabellen-API von Azure Cosmos DB](table-introduction.md) vier SDKs zur Entwicklung verfügbar: 
- .NET SDK [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget). Diese Bibliothek enthält dieselben Klassen- und Methodensignaturen wie das öffentliche [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), kann aber mithilfe der Tabellen-API auch eine Verbindung mit Azure Cosmos DB-Konten herstellen. Hinweis: Die `Microsoft.Azure.CosmosDB.Table`-Bibliothek ist derzeit nur für .NET Standard verfügbar, für .NET Core noch nicht.
- [Python SDK](table-sdk-python.md). Das neue Azure Cosmos DB Python SDK ist das einzige SDK, das Azure Table Storage in Python unterstützt. Dieses SDK stellt eine Verbindung mit Azure Table Storage sowie mit der Tabellen-API von Azure Cosmos DB her.
- [Java SDK](table-sdk-java.md). Dieses Azure Storage SDK kann mithilfe der Tabellen-API eine Verbindung mit Azure Cosmos DB-Konten herstellen.
- [Node.js SDK](table-sdk-nodejs.md). Dieses Azure Storage SDK kann mithilfe der Tabellen-API eine Verbindung mit Azure Cosmos DB-Konten herstellen.

Weitere Informationen zum Arbeiten mit der Tabellen-API finden Sie im FAQ-Artikel unter [ Tabellen-API](faq.md#table).

## <a name="developing-with-azure-table-storage"></a>Entwickeln mit Azure Table Storage

In Azure Table Storage stehen die folgenden SDKs für die Entwicklung zur Verfügung:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Diese Bibliothek ermöglicht Ihnen die Verwendung des Table Storage-Diensts.
- [Python SDK](table-sdk-python.md). Das Azure Cosmos DB Table SDK für Python unterstützt den Table Storage-Dienst ebenfalls.
- [Azure Storage-SDK für Java](https://github.com/azure/azure-storage-java). Dieses Azure Storage SDK enthält eine Clientbibliothek in Java für die Nutzung von Azure Table Storage.
- [Node.js SDK](table-sdk-nodejs.md). Dieses SDK bietet ein Node.js-Paket und eine browserkompatible JavaScript-Clientbibliothek für die Nutzung des Table Storage-Diensts.
- [AzureRmStorageTable-PowerShell-Modul](https://www.powershellgallery.com/packages/AzureRmStorageTable/1.0.0.7). Dieses PowerShell-Modul enthält Cmdlets für die Verwendung mit Speichertabellen.
- [Azure Storage-Clientbibliothek für C++](https://github.com/Azure/azure-storage-cpp/). Diese Clientbibliothek ermöglicht Ihnen die Erstellung von Anwendungen für Azure Storage.
- [Azure Table Storage-Clientbibliothek für Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Dieses Projekt stellt ein Ruby-Paket bereit, das den Zugriff auf Azure Table Storage-Dienste erleichtert.
- [Azure Table Storage-Clientbibliothek für PHP](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Dieses Projekt stellt eine PHP-Clientbibliothek bereit, die den Zugriff auf Azure Table Storage-Dienste erleichtert.


   





