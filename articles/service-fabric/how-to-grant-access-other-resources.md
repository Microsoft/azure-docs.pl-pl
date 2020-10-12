---
title: Udzielanie aplikacji dostępu do innych zasobów platformy Azure
description: W tym artykule wyjaśniono, jak udzielić zarządzanej tożsamości Service Fabric dostępu do innych zasobów platformy Azure obsługujących uwierzytelnianie oparte na Azure Active Directory.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: a1d963c49f9522000028b00c138b2e5731a9ccca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333634"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Udzielanie zarządzanej tożsamości aplikacji Service Fabric do zasobów platformy Azure

Aby aplikacja mogła korzystać z tożsamości zarządzanej w celu uzyskania dostępu do innych zasobów, należy przyznać uprawnienia do tej tożsamości w chronionym dostępnym zasobach platformy Azure. Przyznawanie uprawnień jest zazwyczaj akcją zarządzania w ramach "płaszczyzny kontroli" usługi platformy Azure będącej właścicielem chronionego zasobu kierowanego za pośrednictwem Azure Resource Manager, który będzie wymuszać wszelkie stosowne sprawdzanie dostępu oparte na rolach.

Dokładna sekwencja kroków będzie następnie zależała od typu zasobów platformy Azure, do którego uzyskiwany jest dostęp, a także od języka/klienta używanego do udzielania uprawnień. W pozostałej części artykułu przyjęto założenie, że tożsamość przypisana do aplikacji i zawiera kilka typowych przykładów dla wygody użytkownika, ale nie jest to wyczerpujące odwołanie do tego tematu. Zapoznaj się z dokumentacją odpowiednich usług platformy Azure, aby uzyskać aktualne instrukcje dotyczące przyznawania uprawnień.  

## <a name="granting-access-to-azure-storage"></a>Udzielanie dostępu do usługi Azure Storage
Możesz użyć zarządzanej tożsamości aplikacji Service Fabric (w tym przypadku przypisanej do użytkownika), aby pobrać dane z obiektu BLOB usługi Azure Storage. Przyznaj tożsamości wymagane uprawnienia w Azure Portal wykonaj następujące czynności:

1. Przejdź do konta magazynu
2. Kliknij link Kontrola dostępu (IAM) w panelu po lewej stronie.
3. obowiązkowe Sprawdź istniejący dostęp: Wybierz zarządzaną tożsamość systemową lub przypisaną przez użytkownika w kontrolce "Znajdź"; Wybierz odpowiednią tożsamość z listy wyników wynikowych
4. Kliknij pozycję + Dodaj przypisanie roli w górnej części strony, aby dodać nowe przypisanie roli dla tożsamości aplikacji.
W obszarze Rola, z listy rozwijanej wybierz pozycję czytnik danych magazynu obiektów BLOB.
5. Na następnej liście rozwijanej w obszarze Przypisz dostęp do wybierz opcję `User assigned managed identity` .
6. Następnie upewnij się, że odpowiednia subskrypcja znajduje się na liście rozwijanej Subskrypcja, po czym ustaw opcję Grupa zasobów na wartość Wszystkie grupy zasobów.
7. W obszarze Wybierz Wybierz UAI odpowiadający aplikacji Service Fabric a następnie kliknij przycisk Zapisz.

Obsługa zarządzanych tożsamości przypisanych przez system Service Fabric nie obejmuje integracji w Azure Portal; Jeśli aplikacja używa tożsamości przypisanej do systemu, należy najpierw znaleźć identyfikator klienta tożsamości aplikacji, a następnie powtórz powyższe kroki, ale wybierając `Azure AD user, group, or service principal` opcję w kontrolce Znajdź.

## <a name="granting-access-to-azure-key-vault"></a>Udzielanie dostępu Azure Key Vault
Podobnie jak w przypadku uzyskiwania dostępu do magazynu, można wykorzystać zarządzaną tożsamość aplikacji Service Fabric, aby uzyskać dostęp do magazynu kluczy platformy Azure. Procedury udzielania dostępu w Azure Portal są podobne do wymienionych powyżej i nie będą powtarzane w tym miejscu. Różnice można znaleźć na poniższej ilustracji.

![Zasady dostępu Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Poniższy przykład ilustruje udzielanie dostępu do magazynu za pośrednictwem wdrożenia szablonu; Dodaj fragmenty kodu poniżej jako inny wpis pod `resources` elementem szablonu. W przykładzie pokazano, jak uzyskać dostęp do zarówno typów tożsamości przypisanych przez użytkownika, jak i przypisanych do systemu, a także wybrać odpowiednie.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
I dla tożsamości zarządzanych przypisanych przez system:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Aby uzyskać więcej informacji, zobacz temat [magazyny — aktualizacje zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Następne kroki
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
