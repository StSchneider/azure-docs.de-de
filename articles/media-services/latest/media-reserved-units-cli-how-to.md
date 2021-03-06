---
title: Verwenden der CLI zum Skalieren reservierter Einheiten für Medien – Azure | Microsoft-Dokumentation
description: In diesem Thema wird gezeigt, wie Sie die CLI zum Skalieren der Medienverarbeitung mit Azure Media Services verwenden.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2b10de83e00b3668f70461f76634c560bcbea1a4
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133790"
---
# <a name="scaling-media-processing"></a>Skalieren der Medienverarbeitung

Mit Azure Media Services können Sie die Medienverarbeitung in Ihrem Konto skalieren, indem Sie reservierte Einheiten für Medien (Media Reserved Units, MRUs) verwalten. Eine ausführliche Übersicht finden Sie unter [Scaling media processing](../previous/media-services-scale-media-processing-overview.md) (Skalieren der Medienverarbeitung). 

In diesem Artikel wird veranschaulicht, wie Sie [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) zum Skalieren von MRUs verwenden.

> [!NOTE]
> Für die Audioanalyse- und Videoanalyseaufträge, die von Media Services v3 oder Video Indexer ausgelöst werden, wird dringend empfohlen, Ihr Konto mit zehn S3-MRUs bereitzustellen. <br/>Erstellen Sie im [Azure-Portal](https://portal.azure.com/) ein Supportticket, falls Sie mehr als zehn S3-MRUs benötigen.

## <a name="prerequisites"></a>Voraussetzungen 

- Installieren und verwenden Sie die CLI lokal. Dieser Artikel erfordert mindestens Version 2.0 der Azure CLI. Führen Sie `az --version` aus, um herauszufinden, welche Version Sie haben. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). 

    Derzeit funktionieren nicht alle Befehle der [CLI von Media Services v3](https://aka.ms/ams-v3-cli-ref) in Azure Cloud Shell. Es wird empfohlen, die CLI lokal zu verwenden.

- [Erstellen Sie ein Media Services-Konto.](create-account-cli-how-to.md)

## <a name="scale-media-reserved-units-with-cli"></a>Skalieren von reservierten Einheiten für Medien mit der CLI

Mit dem folgenden Befehl [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) werden reservierte Einheiten für Medien im Konto „amsaccount“ festgelegt, indem die Parameter **count** und **type** verwendet werden.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Abrechnung

Ihnen werden die Gebühren basierend auf Anzahl, Typ und Zeitraum der Bereitstellung von MRUs unter Ihrem Konto berechnet. Die Gebühren fallen unabhängig davon an, ob Sie Aufträge ausführen oder nicht. Eine ausführliche Erläuterung finden Sie im Abschnitt mit den häufig gestellten Fragen auf der Seite [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Nächster Schritt

[Analysieren von Videos](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Weitere Informationen

[Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
