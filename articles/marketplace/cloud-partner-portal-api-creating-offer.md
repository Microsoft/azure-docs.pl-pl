---
title: Tworzenie lub modyfikowanie oferty — Azure Marketplace
description: Interfejs API służący do tworzenia nowej oferty lub aktualizowania istniejącej.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87420230"
---
# <a name="create-or-modify-an-offer"></a>Tworzenie lub modyfikowanie oferty

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z usługą i będą nadal działać w centrum partnerskim. Przejście wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [dokumentacji interfejsu API Portal Cloud partner](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie kontynuował pracę po przejściu do Centrum partnerskiego. Interfejsy API CPP powinny być używane tylko dla istniejących produktów, które zostały już zintegrowane przed przejściem do Centrum partnerskiego; nowe produkty powinny używać interfejsów API przekazywania Centrum partnerskiego.

To wywołanie aktualizuje określoną ofertę w przestrzeni nazw wydawcy lub tworzy nową ofertę.

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identyfikatora URI

|  **Nazwa**         |  **Opis**                      |  **Typ danych**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  Identyfikator wydawcy, na przykład `contoso` |   Ciąg |
| offerId           |  Identyfikator oferty                     |   Ciąg        |
| api-version       |  Najnowsza wersja interfejsu API            |   Date           |
|  |  |  |

## <a name="header"></a>Header

|  **Nazwa**        |  **Wartość**               |
|  ---------       |  ----------              | 
| Content-Type     | `application/json`       |
| Autoryzacja    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>Przykład treści

Poniższy przykład tworzy ofertę z offerID `contosovirtualmachine` .

### <a name="request"></a>Żądanie

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>Reakcja

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> Aby zmodyfikować tę ofertę, Dodaj nagłówek **if-Match** o wartości * do powyższego żądania. Użyj tej samej treści żądania jak powyżej, ale Zmodyfikuj wartości zgodnie z potrzebami. 

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kod**  |  **Opis**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. Żądanie zostało pomyślnie przetworzone i oferta została zmodyfikowana pomyślnie.           |
|  201      | `Created`. Żądanie zostało pomyślnie przetworzone i oferta została pomyślnie utworzona.   |
|  400      | `Bad/Malformed request`. Treść odpowiedzi na błąd może dostarczyć więcej informacji.            |
|  403      | `Forbidden`. Klient nie ma dostępu do żądanego obszaru nazw.                     |
|  404      | `Not found`. Jednostka, do której odwołuje się klient, nie istnieje.                           |
|  412      | Serwer nie spełnia jednego z warunków wstępnych określonych w żądaniu. Klient powinien sprawdzić element ETAG wysłany z żądaniem. |
|  |  |

## <a name="uploading-artifacts"></a>Przekazywanie artefaktów

Artefakty, takie jak obrazy i logo, powinny być udostępniane przez przekazanie ich do dostępnej lokalizacji w sieci Web, a następnie dołączenie ich jako identyfikatora URI w żądaniu PUT, jak w powyższym przykładzie. System wykryje, że te pliki nie znajdują się w magazynie Azure Marketplace, i pobierzą te pliki do magazynu.  W związku z tym zobaczysz, że przyszłe żądania GET będą zwracać adres URL usługi Azure Marketplace dla tych plików.

## <a name="categories-and-industries"></a>Kategorie i branże

Podczas tworzenia nowej oferty należy określić kategorię oferty w portalu Marketplace. Opcjonalnie w przypadku niektórych typów ofert można także określić branże. Na podstawie typu oferty podaj kategorie/branże stosowane do oferty przy użyciu określonych wartości klucza z poniższych tabel.

### <a name="azure-marketplace-categories"></a>Kategorie portalu Azure Marketplace

Te kategorie i odpowiednie klucze mają zastosowanie do aplikacji platformy Azure, Virtual Machines, Virtual Machines podstawowych, kontenerów, aplikacji kontenera, modułów IoT Edge i typów ofert SaaS. Elementy pogrubione kursywą (na przykład ***Analytics***) to kategorie i standardowe elementy tekstowe (takie jak dane szczegółowe) są podkategoriami poniżej. Używaj dokładnych wartości klucza bez zmiany odstępów lub wielkości liter.

| Kategoria | Klucze SaaS | Klucze aplikacji platformy Azure | Maszyna wirtualna, kontenery, aplikacje kontenerów, moduł IoT Edge, podstawowe klucze maszyn wirtualnych |
| --- | --- | --- | --- |
| ***Analiza*** | ***wersję*** | ***Analiza — Azure — aplikacje*** | ***Analiza — amp*** |
| Wgląd w dane | dane szczegółowe | dane szczegółowe | dane szczegółowe |
| Analiza danych | Analiza danych | Analiza danych | Analiza danych |
| Dane big data | duże ilości danych | bigData | duże ilości danych |
| Analiza predykcyjna | Analiza predykcyjna | Analiza predykcyjna | Analiza predykcyjna |
| Analiza czasu rzeczywistego/przesyłania strumieniowego | przesyłanie strumieniowe — analiza w czasie rzeczywistym | przesyłanie strumieniowe — analiza w czasie rzeczywistym | przesyłanie strumieniowe — analiza w czasie rzeczywistym |
| Inne | other | inne — analiza | other |
| ***SI i uczenie maszynowe*** | ***ArtificialIntelligence*** | ***AI-Plus — Uczenie maszynowe*** | ***AI-Plus — Uczenie maszynowe*** |
| Usługi bot Services | Bot — usługi | Bot — usługi | Bot — usługi |
| Cognitive Services | poznawcze — usługi | poznawcze — usługi | poznawcze — usługi |
| Usługa ML | ml — usługa | ml — usługa | ml — usługa |
| Zautomatyzowane uczenie maszynowe | zautomatyzowane-ml | zautomatyzowane-ml | zautomatyzowane-ml |
| Automatyzacja procesów biznesowych/zautomatyzowanych | biznes-Robotic-Process-Automation | biznes-Robotic-Process-Automation | biznes-Robotic-Process-Automation |
| Etykietowanie danych | etykietowanie danych | etykietowanie danych | etykietowanie danych |
| Przygotowywanie danych | Przygotowywanie danych | Przygotowywanie danych | Przygotowywanie danych |
| Wyszukiwanie merytoryczne | Baza wiedzy — wyszukiwanie | Baza wiedzy — wyszukiwanie | Baza wiedzy — wyszukiwanie |
| Operacje ML | ml — operacje | ml — operacje | ml — operacje |
| Inne | inne-AI-Plus — Uczenie maszynowe | other | other |
| ***Łańcuch bloków*** | ***łańcucha bloków*** | ***łańcucha bloków*** | ***łańcucha bloków*** |
| Akceleratory aplikacji | Akceleratory aplikacji | Akceleratory aplikacji | Akceleratory aplikacji |
| Księga z jednym węzłem | Księga jednowęzłowa | Księga jednowęzłowa | Księga jednowęzłowa |
| Księga wielowęzłowa | Księga wielowęzłowa | Księga wielowęzłowa | Księga wielowęzłowa |
| Narzędzia | tools | tools | tools |
| Inne | other | other | other |
| ***Środowisko obliczeniowe*** | ***obliczenia — SaaS*** | ***obliczenia — Azure — aplikacje*** | ***obliczeniowych*** |
| Infrastruktura aplikacji | appInfra | appInfrastructure | Infrastruktura aplikacji |
| Systemy operacyjne | clientOS | clientOS | systemy operacyjne |
| Pamięć podręczna | cache | cache | cache |
| Inne | inne — obliczenia | inne — obliczenia | other |
| ***Containers*** | ***opakowania*** | ***opakowania*** | ***opakowania*** |
| Aplikacje kontenera | kontener — aplikacje | kontener — aplikacje | kontener — aplikacje |
| Obrazy kontenerów | kontener — obrazy | kontener — obrazy | kontener — obrazy |
| Wprowadzenie do kontenerów | Get-Started-with-Containers | Get-Started-with-Containers | Get-Started-with-Containers |
| Inne | other | other | other |
| ***Bazy danych*** | ***bazy danych — SaaS*** | ***Database*** | ***6.5*** |
| Bazy danych NoSQL | NoSQL — bazy danych | NoSQL — bazy danych | NoSQL — bazy danych |
| Relacyjne bazy danych | relacyjne — bazy danych | relacyjne — bazy danych | relacyjne — bazy danych |
| Bazy danych księgi/łańcucha bloków | Ledger-łańcucha bloków — bazy danych | Ledger-łańcucha bloków — bazy danych | Ledger-łańcucha bloków — bazy danych |
| Jeziora dotyczące danych | dane — jeziora | dane — jeziora | dane — jeziora |
| programu Operations Manager | Magazyn danych | Magazyn danych | Magazyn danych |
| Inne | inne — bazy danych | inne — bazy danych | other |
| ***Narzędzia deweloperskie*** | ***Developer Tools — SaaS*** | ***Developer-Tools-Azure — aplikacje*** | ***Narzędzia dla deweloperów*** |
| Narzędzia | Narzędzia — Developer Tools | Narzędzia — Developer Tools | Narzędzia — Developer Tools |
| Skrypty | skrypty | skrypty | skrypty |
| Usługa dla deweloperów | devService | devService | Deweloper — usługa |
| Inne | Inne — narzędzia dla deweloperów | Inne — narzędzia dla deweloperów | other |
| ***DevOps*** | ***DevOps*** | ***DevOps*** | ***DevOps*** |
| Inne | other | other | other |
| ***Tożsamość*** | ***Identity*** | ***Identity*** | ***Identity*** |
| Zarządzanie dostępem | Zarządzanie dostępem | Zarządzanie dostępem | Zarządzanie dostępem |
| Inne | other | other | other |
| ***Integracja*** | ***Integration*** | ***Integration*** | ***Integration*** |
| Obsługa komunikatów | przesyłanie | przesyłanie | przesyłanie |
| Inne | other | other | other |
| ***Internet rzeczy*** | ***IoT*** | ***Internet-rzeczy — Azure — aplikacje*** | ***Internet-rzeczy*** |
| Usługi IoT Core | Nie dotyczy | IoT-Core-Services | IoT-Core-Services |
| Moduły IoT Edge | Nie dotyczy | IoT — moduły | IoT — moduły |
| Rozwiązania IoT | IoT — rozwiązania | IoT — rozwiązania | IoT — rozwiązania |
| Wizualizacja & analizy danych | dane — analiza i wizualizacja | dane — analiza i wizualizacja | dane — analiza i wizualizacja |
| Łączność IoT | IoT — łączność | IoT — łączność | IoT — łączność |
| Inne | inne — Internet-rzeczy | inne — Internet-rzeczy | other |
| ***Narzędzia do zarządzania & IT*** | ***ITandAdministration*** | ***IT-and-Management-Tools-Azure — aplikacje*** | ***narzędzia IT i Management Tools*** |
| Rozwiązania do zarządzania | Zarządzanie — rozwiązania | Zarządzanie — rozwiązania | Zarządzanie — rozwiązania |
| Aplikacje biznesowe | businessApplication | businessApplication | aplikacje biznesowe |
| Inne | Inne — narzędzia do zarządzania | Inne — narzędzia do zarządzania | other |
| ***Monitorowanie diagnostyki &*** | ***monitorowanie i Diagnostyka*** | ***monitorowanie i Diagnostyka*** | ***monitorowanie i Diagnostyka*** |
| Inne | other | other | other |
| ***Media*** | ***multimedialny*** | ***multimedialny*** | ***multimedialny*** |
| Media Services | media-services | media-services | media-services |
| Content Protection | Ochrona zawartości | Ochrona zawartości | Ochrona zawartości |
| Przesyłanie strumieniowe na żywo & na żądanie | przesyłanie strumieniowe na żywo i na żądanie | przesyłanie strumieniowe na żywo i na żądanie | przesyłanie strumieniowe na żywo i na żądanie |
| Inne | other | other | other |
| ***Migracja*** | ***migracji*** | ***migracji*** | ***migracji*** |
| Migrowanie danych | Migracja danych | Migracja danych | Migracja danych |
| Inne | other | other | other |
| ***Rzeczywistość mieszana*** | ***rzeczywistość mieszana*** | ***rzeczywistość mieszana*** | ***rzeczywistość mieszana*** |
| Inne | other | other | other |
| ***Sieć*** | ***sieci*** | ***sieci*** | ***sieci*** |
| Menedżerowie urządzeń | urządzenie — menedżerowie | urządzenie — menedżerowie | urządzenie — menedżerowie |
| Łączność | łączność | łączność | łączność |
| Zapory | zapory | zapory | zapory |
| Moduły równoważenia obciążenia | usługi równoważenia obciążenia | usługi równoważenia obciążenia | usługi równoważenia obciążenia |
| Inne | other | other | other |
| ***Zabezpieczenia*** | ***bezpieczeństw*** | ***bezpieczeństw*** | ***bezpieczeństw*** |
| Zarządzanie dostępem & tożsamości | Zarządzanie tożsamościami i dostępem | Zarządzanie tożsamościami i dostępem | Zarządzanie tożsamościami i dostępem |
| Ochrona przed zagrożeniami | Ochrona przed zagrożeniami | Ochrona przed zagrożeniami | Ochrona przed zagrożeniami |
| Information Protection | informacje — ochrona | informacje — ochrona | informacje — ochrona |
| Inne | other | other | other |
| ***Storage*** | ***Magazyn — SaaS*** | ***Magazyn — Azure — aplikacje*** | ***Chowan*** |
| Odzyskiwanie & kopii zapasowej | kopia zapasowa | kopia zapasowa | Tworzenie kopii zapasowych i odzyskiwanie |
| Magazyn hybrydowy przedsiębiorstwa | przedsiębiorstwo — magazyn hybrydowy | przedsiębiorstwo — magazyn hybrydowy | przedsiębiorstwo — magazyn hybrydowy |
| Udostępnianie plików | Udostępnianie plików | Udostępnianie plików | Udostępnianie plików |
| Zarządzanie cyklem życia danych | cykl życia danych — zarządzanie | cykl życia danych — zarządzanie | cykl życia danych — zarządzanie |
| Inne | inne magazyny | inne magazyny | other |
| ***Sieć Web*** | ***witrynę*** | ***witrynę*** | ***witrynę*** |
| Blogi & CMS | Blogi i CMS | Blogi i CMS | Blogi i CMS |
| Web Apps Starter | Starter — aplikacje sieci Web | Starter — aplikacje sieci Web | Starter — aplikacje sieci Web |
| Handlu elektronicznego | handlu elektronicznego | handlu elektronicznego | handlu elektronicznego |
| Struktury Web Apps | aplikacje sieci Web — platformy | aplikacje sieci Web — platformy | aplikacje sieci Web — platformy |
| Web Apps | aplikacje sieci Web | aplikacje sieci Web | aplikacje sieci Web |
| Inne | other | other | other |
||||

### <a name="microsoft-appsource-categories"></a>Kategorie Microsoft AppSource

Te kategorie i ich odpowiednie klucze są stosowane do aplikacji SaaS, Power BI, Dynamics 365 Business Central, Dynamics 365 for Customer Engagement i Dynamics 365 dla typów ofert operacji. Elementy pogrubione kursywą (na przykład ***Analytics***) to kategorie i standardowe elementy tekstowe (na przykład zaawansowane-Analytics) są podkategoriami poniżej. Używaj dokładnych wartości klucza bez zmiany odstępów lub wielkości liter.

| Kategoria | Klucze SaaS | Dynamics 365 Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 dla kluczy operacji | Klucze aplikacji usługi PowerBI |
| --- | --- | --- | --- |
| ***Analiza*** | ***wersję*** | ***Analiza*** | ***Analiza*** |
| Analiza zaawansowana | Zaawansowane — analiza | Zaawansowane — analiza | Zaawansowane — analiza |
| Wizualizacja & raporty | Wizualizacja — raportowanie | Wizualizacja — raportowanie | Wizualizacja — raportowanie |
| Inne | other | inne — analiza | inne — analiza |
| ***SI i uczenie maszynowe*** | ***ArtificialIntelligence*** | ***AI-Plus-Machine-Learning-Dynamics*** | ***AI-Plus-Machine-Learning-AppSource*** |
| AI dla firm | AI — dla firm | AI — dla firm | AI — dla firm |
| Aplikacje bot | Bot — aplikacje | Bot — aplikacje | Bot — aplikacje |
| Inne | inne-AI-Plus — Uczenie maszynowe | inne-AI-Plus — Uczenie maszynowe | inne-AI-Plus — Uczenie maszynowe |
| ***Współpraca*** | ***Współpraca*** | ***Współpraca*** | ***społeczności*** |
| Kontakt & osób | kontakt — osoby | kontakt — osoby | Kontakty i osoby |
| Zarządzanie spotkaniami | spotkanie — zarządzanie | spotkanie — zarządzanie | spotkanie — zarządzanie |
| Zarządzanie & projektu lokacji | Lokacja-projektowanie — zarządzanie | Lokacja-projektowanie — zarządzanie | Lokacja-projektowanie i zarządzanie |
| Zarządzanie projektem & zadań | zadanie — zarządzanie projektem | zadanie — zarządzanie projektem | Zarządzanie zadaniami i projektami |
| Konferencje wideo & głosu | Voice-wideo — konferencje | Voice-wideo — konferencje | połączenia głosowe i wideo — konferencje |
| Inne | inna współpraca | inna współpraca | other |
| ***Zgodność & prawny*** | ***przepisów*** | ***przepisów*** | ***zgodność i prawa*** |
| Inspekcja & podatkowych | podatek-Inspekcja | podatek-Inspekcja | podatki i inspekcja |
| Informacje prawne | Informacje prawne | Informacje prawne | prawidłowe |
| Dane, nadzór & prywatność | dane — nadzór — Ochrona prywatności | dane — nadzór — Ochrona prywatności | dane — nadzór i ochrona prywatności |
| Kondycja & | Kondycja — bezpieczeństwo | Kondycja — bezpieczeństwo | Kondycja i bezpieczeństwo |
| Inne | inne-zgodność — informacje prawne | inne-zgodność — informacje prawne | other |
| ***Customer Service*** | ***CustomerService*** | ***CustomerService*** | ***Klient-usługa*** |
| Centrum kontaktów | kontakt — centrum | kontakt — centrum | kontakt — centrum |
| Usługa do rozpoznawania | Tworzenie i obsługa czołowa | Tworzenie i obsługa czołowa | Tworzenie i obsługa czołowa |
| Zaplecze usługi Office & Employee | zaplecze — pracownik-usługa | zaplecze — pracownik-usługa | Obsługa zaplecza i pracowników |
| Zarządzanie sprawami & wiedzy | przypadek wiedzy — zarządzanie | przypadek wiedzy — zarządzanie | znajomość i sprawa — zarządzanie |
| Media społecznościowe & omnichannel Engagement | Social-Media-omnichannel — Engagement | Social-Media-omnichannel — Engagement | społecznościowe i omnichannel — zaangażowanie |
| Inne | inne — klient — usługa | inne — klient — usługa | other |
| ***Finanse*** | ***Finanse*** | ***Finanse*** | ***odsetek*** |
| Księgowość | accounting | accounting | accounting |
| Zarządzanie zasobami | Zarządzanie zasobami | Zarządzanie zasobami | Zarządzanie zasobami |
| Analiza, konsolidacja & raportowanie | Analiza — konsolidacja — raportowanie | Analiza — konsolidacja — raportowanie | Analiza — Konsolidacja i raportowanie |
| Kredyty &owe | Kredyty — Kolekcje | Kredyty — Kolekcje | kredyty i kolekcje |
| Zgodność & zarządzanie ryzykiem | zgodność — zarządzanie ryzykiem | zgodność — zarządzanie ryzykiem | zgodność i zarządzanie ryzykiem |
| Inne | inne — finanse | inne — finanse | other |
| ***Human Resources*** | ***HumanResources*** | ***HumanResources*** | ***zasoby ludzkie*** |
| Talent pozyskiwanie | talent — pozyskiwanie | talent — pozyskiwanie | talent — pozyskiwanie |
| Talent Management | talent — zarządzanie | talent — zarządzanie | talent — zarządzanie |
| Operacje na HR | HR — operacje | HR — operacje | HR — operacje |
| Planowanie pracowników & Analytics | pracownicy — planowanie — analiza | pracownicy — planowanie — analiza | pracownicy — planowanie i analiza |
| Inne | inne zasoby kadrowe | inne zasoby kadrowe | other |
| ***Internet rzeczy*** | ***IoT*** | ***Internet-rzeczy — Dynamics*** | ***Internet-rzeczy — AppSource*** |
| Operacje & zarządzania zasobami | Zarządzanie zasobami — operacje | Zarządzanie zasobami — operacje | Zarządzanie zasobami i operacje |
| Połączone produkty | połączone produkty | połączone produkty | połączone produkty |
| Inteligentny łańcuch dostaw | inteligentny — łańcuch dostaw | inteligentny — łańcuch dostaw | inteligentny — łańcuch dostaw |
| Konserwacja zapobiegawcza | predykcyjne — konserwacja | predykcyjne — konserwacja | predykcyjne — konserwacja |
| Zdalne monitorowanie | zdalne monitorowanie | zdalne monitorowanie | zdalne monitorowanie |
| Bezpieczeństwo & zabezpieczenia | bezpieczeństwo — zabezpieczenia | bezpieczeństwo — zabezpieczenia | bezpieczeństwo i zabezpieczenia |
| Infrastruktura inteligentna & zasoby | Infrastruktura inteligentna — zasoby | Infrastruktura inteligentna — zasoby | Inteligentne infrastruktury i zasoby |
| Pojazdy & Mobility | Pojazdy — mobilność | Pojazdy — mobilność | pojazdy i mobilność |
| Inne | inne — Internet-rzeczy | inne — Internet-rzeczy | other |
| ***Narzędzia do zarządzania & IT*** | ***ITandAdministration*** | ***ITandAdministration*** | ***narzędzia IT i Management Tools*** |
| Rozwiązania do zarządzania | Zarządzanie — rozwiązania | Zarządzanie — rozwiązania | Zarządzanie — rozwiązania |
| Aplikacje biznesowe | businessApplication | businessApplication | aplikacje biznesowe |
| Inne | Inne — narzędzia do zarządzania | Inne — narzędzia do zarządzania | other |
| ***Marketing*** | ***Marketing*** | ***Marketing*** | ***rynkow*** |
| Nagłówek | Nagłówek | Nagłówek | Nagłówek |
| Analiza | Analiza — Marketing | Analiza — Marketing | Analiza — Marketing |
| Automatyzacja & zarządzania kampaniami | Kampania — zarządzanie — Automatyzacja | Kampania — zarządzanie — Automatyzacja | Kampania — zarządzanie i Automatyzacja |
| Marketing poczty e-mail | Poczta e-mail — marketing | Poczta e-mail — marketing | Poczta e-mail — marketing |
| L2 — zdarzenia & zarządzanie zasobami | zdarzenia — zarządzanie zasobami | zdarzenia — zarządzanie zasobami | zdarzenia i zarządzanie zasobami |
| Analiza & Research | badania — analiza | badania — analiza | badania i analiza |
| Media społecznościowe | Media społecznościowe | Media społecznościowe | Media społecznościowe |
| Inne | inne — Marketing | inne — Marketing | other |
| ***Łańcuch dostaw & operacji*** | ***OperationsSupplyChain*** | ***OperationsSupplyChain*** | ***operacje — łańcuch i dostawy*** |
| Zarządzanie zasobami & produkcji | zasób — zarządzanie produkcją | zasób — zarządzanie produkcją | Zarządzanie zasobami i produkcjami |
| Prognozowanie popytu | prognozowanie popytu | prognozowanie popytu | prognozowanie popytu |
| Zarządzanie informacjami & łączności | informacje — zarządzanie — łączność | informacje — zarządzanie — łączność | informacje — zarządzanie i łączność |
| Planowanie, kupowanie & raportowania | Planowanie — zakupy — raportowanie | Planowanie — zakupy — raportowanie | Planowanie — kupowanie i raportowanie |
| & zarządzanie usługami | jakość — Usługa — Zarządzanie | jakość — Usługa — Zarządzanie | jakość i Usługa — Zarządzanie |
| Zarządzanie zamówieniami & sprzedaży | sprzedaż — Zarządzanie zamówieniami | sprzedaż — Zarządzanie zamówieniami | Sales-and-Order-Management |
| Transport & Zarządzanie magazynem | Transport — Magazyn — Zarządzanie | Transport — Magazyn — Zarządzanie | Transport i Magazyn — Zarządzanie |
| Inne | inne — łańcuch dostaw operacji | inne — łańcuch dostaw operacji | other |
| ***Produktywność*** | ***Produktywność*** | ***Produktywność*** | ***zwiększając*** |
| Tworzenie zawartości & zarządzanie | Tworzenie zawartości — zarządzanie | Tworzenie zawartości — zarządzanie | Tworzenie zawartości i zarządzanie nimi |
| Tłumaczenie & języka | tłumaczenie języka | tłumaczenie języka | język i tłumaczenie |
| Zarządzanie dokumentami | Zarządzanie dokumentami | Zarządzanie dokumentami | Zarządzanie dokumentami |
| Zarządzanie pocztą e-mail | Zarządzanie pocztą e-mail | Zarządzanie pocztą e-mail | Zarządzanie pocztą e-mail |
| Wyszukaj informacje & | Wyszukiwanie — odwołanie | Wyszukiwanie — odwołanie | Wyszukiwanie i odwołania |
| Inne | inna produktywność | inna produktywność | other |
| Grywalizacja | Grywalizacja | Grywalizacja | grywalizacji |
| ***Sales*** | ***Sales*** | ***Sales*** | ***Sales*** |
| Sprzedaż | sprzedaż | sprzedaż | sprzedaż |
| Konfigurowanie, Cena, oferta (CPQ) | Konfiguracja — Cena oferty | Konfiguracja — Cena oferty | Konfiguracja — Cena oferty |
| Zarządzanie kontraktami | kontrakt — zarządzanie | kontrakt — zarządzanie | kontrakt — zarządzanie |
| CRM | crm | crm | crm |
| Handel elektroniczny | handel elektroniczny | handel elektroniczny | handel elektroniczny |
| Wzbogacanie danych firmowych | biznes — Wzbogacanie danych | biznes — Wzbogacanie danych | biznes — Wzbogacanie danych |
| Włączenie sprzedaży | sprzedaż — Włączanie | sprzedaż — Włączanie | sprzedaż — Włączanie |
| Inne | inne — sprzedaż | inne — sprzedaż | inne — sprzedaż |
| ***Geolokalizacja*** | ***Geolokalizacja*** | ***Geolokalizacja*** | ***Geolokalizacja*** |
| Maps | maps | maps | maps |
| Wiadomości & Pogoda | wiadomości i Pogoda | wiadomości i Pogoda | wiadomości i Pogoda |
| Inne | inne-geolokalizacja | inne-geolokalizacja | inne-geolokalizacja |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource branże

Te branże i ich odpowiednie klucze mają zastosowanie do aplikacji SaaS, Power BI, Dynamics 365 Business Central, Dynamics 365 for Customer Engagement i Dynamics 365 dla typów ofert operacji. Elementy pogrubione kursywą (na przykład ***motoryzacyjne***) to kategorie i standardowe elementy tekstowe (takie jak AutomotiveL2) są podkategoriami poniżej nich. Używaj dokładnych wartości klucza bez zmiany odstępów lub wielkości liter.

| Branża | SaaS, Dynamics 365 Business Central, Dynamics 365 dla zaangażowania klientów, Dynamics 365 dla kluczy operacji | Klucze aplikacji usługi PowerBI |
| --- | --- | --- |
| ***Motoryzacja*** | ***Motoryzacja*** | ***przemysł samochodowy*** |
| Motoryzacja | AutomotiveL2 | AutomotiveL2 |
| ***Rolnictwo*** | ***Rolnictwo*** | ***rolnictwa*** |
| Inne — niesegmentacja | \_OtherUnsegmented rolnictwo | inne — niesegmentacja |
| ***Dystrybucja*** | ***Dystrybucja*** | ***mieszczenie*** |
| Handel hurtowy | Handel hurtowy | handel hurtowy |
| Wysyłka pakietu & Parcel | ParcelAndPackageShipping | Pakowanie i paczka — wysyłka |
| ***Edukacja*** | ***Edukacja*** | ***oświat*** |
| Szkolnictwo wyższe | HigherEducation | wyższe — Edukacja |
| Podstawowe & szkolnictwa wtórnego/K-12 | PrimaryAndSecondaryEducationK12 | podstawowe i pomocnicze — Edukacja |
| Biblioteki & muzea | LibrariesAndMuseums | biblioteki i muzea |
| ***Usługi finansowe*** | ***FinancialServices*** | ***usługi finansowe*** |
| Bankowość & — rynki kapitałowe | BankingAndCapitalMarkets | inwestycje i kapitały inwestycyjne |
| Zakład | Zakład | zakład |
| ***Instytucje rządowe*** | ***Instytucje rządowe*** | ***Zarządowi*** |
| Inteligencja & obrony | DefenseAndIntelligence | Ochrona i analiza |
| Bezpieczeństwo publiczne & sprawiedliwości | PublicSafetyAndJustice | bezpieczeństwo i sprawiedliwości |
| Rząd cywilny | CivilianGovernment | cywilne-rządowe |
| ***Opieka zdrowotna*** | ***HealthCareandLifeSciences*** | ***zdrowotne*** |
| Płatnik kondycji | HealthPayor | Kondycja — płatnik |
| Dostawca kondycji | HealthProvider | Kondycja — dostawca |
| Pharmaceuticals | Pharmaceuticals | Pharmaceuticals |
| ***Zasoby & produkcyjnego*** | ***Produkcja*** | ***Produkcja i zasoby*** |
| Chemical & Agrochemical | ChemicalAndAgrochemical | chemiczne i Agrochemical |
| Produkcja dyskretna | DiscreteManufacturing | dyskretna produkcja |
| Energetyczna | Energetyczna | energy |
| ***Towary konsumenckie detaliczne &*** | ***RetailandConsumerGoods*** | ***sprzedaż detaliczna i odbiorcy*** |
| Towary konsumenckie | ConsumerGoods | odbiorcy |
| Detalicznych | Detalicznych | detalicznych |
| ***Komunikacja & Media*** | ***MediaAndCommunications*** | ***Multimedia i komunikacja*** |
| Multimedia & rozrywki | MediaandEntertainment | Multimedia i rozrywka |
| Telekomunikacja | Telekomunikacja | komunikacyjny |
| ***Profesjonalne usługi*** | ***ProfessionalServices*** | ***profesjonalne — usługi*** |
| Informacje prawne | Informacje prawne | prawidłowe |
| Profesjonalne usługi partnerskie | PartnerProfessionalServices | Partner — profesjonalne usługi |
| ***Architektura & konstrukcja*** | ***ArchitectureAndConstruction*** | ***Architektura i konstrukcja*** |
| Inne — niesegmentacja | ArchitectureAndConstruction \_ OtherUnsegmented | inne — niesegmentacja |
| ***Hotelarstwo & podróży*** | ***HospitalityandTravel*** | ***Hotelarstwo i podróże*** |
|    Hotele & wypoczynek | HotelsAndLeisure | Hotele i wypoczynek |
| Transport & podróże | TravelAndTransportation | Podróże i transport |
| Restauracje & żywność Services | RestaurantsAndFoodServices | Restauracje i żywność — usługi |
| ***Inne branże sektora publicznego*** | ***OtherPublicSectorIndustries*** | ***sektory inne-publiczne-branżowe*** |
| Leśnictwo & rybołówstwo | ForestryAndFishing | Leśnictwo i rybołówstwo |
| Organizacji niedochodowych | Organizacji niedochodowych | organizacji niedochodowych |
| ***Nieruchomości*** | ***RealEstate*** | ***rzeczywista*** |
| Inne — niesegmentacja | RealEstate \_ OtherUnsegmented | inne — niesegmentacja |
|||
