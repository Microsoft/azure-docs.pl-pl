---
title: Azure AD Connect przekształcenia aprowizacji w chmurze
description: W tym artykule opisano sposób użycia transformacji w celu zmiany domyślnych mapowań atrybutów.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: a15a25ce4fbe1a43c2a5065bf88f5c40c5865721
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85360694"
---
# <a name="transformations"></a>Przekształcenia

Za pomocą transformacji można zmienić domyślne zachowanie tego, jak atrybut jest synchronizowany z Azure Active Directory (Azure AD) przy użyciu aprowizacji w chmurze.

Aby wykonać to zadanie, należy edytować schemat, a następnie przesłać go ponownie za pośrednictwem żądania sieci Web.

Aby uzyskać więcej informacji na temat atrybutów aprowizacji w chmurze, zobacz [Omówienie schematu usługi Azure AD](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Pobierz schemat
Aby pobrać schemat, wykonaj kroki opisane w temacie [Wyświetl schemat](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Mapowanie atrybutów niestandardowych
Aby dodać niestandardowe Mapowanie atrybutów, wykonaj następujące kroki.

1. Skopiuj schemat do tekstu lub edytora kodu, takiego jak [Visual Studio Code](https://code.visualstudio.com/).
1. Zlokalizuj obiekt, który chcesz zaktualizować w schemacie.

   ![Obiekt w schemacie](media/how-to-transformation/transform1.png)</br>
1. Znajdź kod dla `ExtensionAttribute3` obiektu użytkownika.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Edytuj kod, tak aby atrybut Company został zamapowany na `ExtensionAttribute3` .

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Skopiuj schemat z powrotem do Eksploratora grafów, Zmień **Typ żądania** na **Put**i wybierz pozycję **Uruchom zapytanie**.

    ![Uruchamianie zapytania](media/how-to-transformation/transform2.png)

 1. Teraz w Azure Portal przejdź do konfiguracji aprowizacji w chmurze, a następnie wybierz pozycję **Uruchom ponownie Inicjowanie obsługi administracyjnej**.

    ![Ponowne uruchamianie aprowizacji](media/how-to-transformation/transform3.png)

 1. Za chwilę Sprawdź, czy atrybuty są wypełniane, uruchamiając następujące zapytanie w Eksploratorze grafu: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` .
 1. Powinna zostać wyświetlona wartość.

    ![Zostanie wyświetlona wartość](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mapowanie atrybutów niestandardowych za pomocą funkcji
Aby uzyskać bardziej zaawansowane mapowanie, można użyć funkcji, które umożliwiają manipulowanie danymi i tworzenie wartości atrybutów, aby odpowiadały potrzebom organizacji.

Aby wykonać to zadanie, wykonaj poprzednie kroki, a następnie Edytuj funkcję, która jest używana do konstruowania wartości końcowej.

Aby uzyskać informacje na temat składni i Przykłady wyrażeń, zobacz [Pisanie wyrażeń pod kątem mapowań atrybutów w Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
