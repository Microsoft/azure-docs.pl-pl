---
title: Znane problemy z systemem na potrzeby zgodności między domenami (standard scim) 2,0 zgodność protokołu — Azure AD
description: Jak rozwiązać typowe problemy ze zgodnością protokołu podczas dodawania aplikacji spoza galerii, która obsługuje standard scim 2,0 do usługi Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: d13629b4cb05995b9652e862f769a0ffcae30a8c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256902"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Znane problemy i rozwiązania z standard scim 2,0 zgodności protokołów aprowizacji użytkowników usługi Azure AD

Azure Active Directory (Azure AD) mogą automatycznie inicjować obsługę użytkowników i grup w dowolnej aplikacji lub systemie, który jest przedsunięty przez usługę sieci Web, przy użyciu interfejsu zdefiniowanego w systemie w ramach [specyfikacji protokołu zarządzania tożsamościami (standard scim 2,0) dla wielu domen](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Obsługa protokołu Standard scim 2,0 w usłudze Azure AD jest opisana w temacie [Używanie systemu do zarządzania tożsamościami między domenami (standard scim) w celu automatycznego aprowizacji użytkowników i grup z Azure Active Directoryymi do aplikacji](use-scim-to-provision-users-and-groups.md), które zawierają listę konkretnych części protokołu, które implementuje w celu automatycznego aprowizacji użytkowników i grup z usługi Azure AD do aplikacji obsługujących standard scim 2,0.

W tym artykule opisano bieżące i wcześniejsze problemy związane z przystąpieniem usługi Azure AD User Provisioning do protokołu Standard scim 2,0 i obejście tego problemu.

## <a name="understanding-the-provisioning-job"></a>Informacje o zadaniu aprowizacji
Usługa aprowizacji używa koncepcji zadania do działania względem aplikacji. Identyfikator zadania można znaleźć na [pasku postępu](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar). Wszystkie nowe aplikacje aprowizacji są tworzone z identyfikatorem jobID rozpoczynającym się od "Standard scim". Zadanie Standard scim reprezentuje bieżący stan usługi. Starsze zadania mają identyfikator "customappsso". To zadanie reprezentuje stan usługi w 2018. 

Jeśli używasz aplikacji w galerii, zadanie zazwyczaj zawiera nazwę aplikacji (np. Powiększanie płatnych śniegów, elementów datakostki itp.). Tę dokumentację można pominąć w przypadku korzystania z aplikacji galerii. Dotyczy to głównie aplikacji nienależących do galerii o identyfikatorze jobID Standard scim lub customAppSSO.

## <a name="scim-20-compliance-issues-and-status"></a>Problemy ze zgodnością i stanem Standard scim 2,0
W poniższej tabeli każdy element oznaczony jako FIXED oznacza, że odpowiednie zachowanie można znaleźć w zadaniu Standard scim. Firma Microsoft pracowała nad zapewnieniem zgodności z poprzednimi wersjami dla wprowadzonych zmian. Nie zaleca się jednak implementowania starego zachowania. Zalecamy używanie nowego zachowania dla nowych implementacji i aktualizowanie istniejących implementacji.

> [!NOTE]
> W przypadku zmian wprowadzonych w 2018 można przywrócić zachowanie customappsso. W przypadku zmian wprowadzonych od 2018 można użyć adresów URL do przywrócenia starszego zachowania. Firma Microsoft pracowała nad zapewnieniem zgodności z poprzednimi wersjami wprowadzonymi przez nas, umożliwiając przywrócenie starego identyfikatora jobID lub użycie flagi. Jednak jak wspomniano wcześniej, nie zalecamy implementowania starego zachowania. Zalecamy używanie nowego zachowania dla nowych implementacji i aktualizowanie istniejących implementacji.

| **Problem ze zgodnością w programie Standard scim 2,0** |  **FIXED?** | **Popraw datę**  |  **Zgodność z poprzednimi wersjami** |
|---|---|---|
| Usługa Azure AD wymaga "/scim", aby znajdować się w katalogu głównym adresu URL punktu końcowego Standard scim aplikacji  | Tak  |  18 grudnia, 2018 | Obniżanie poziomu do customappSSO |
| Atrybuty rozszerzenia używają notacji kropki "." przed nazwami atrybutów zamiast dwukropka ":" notacji |  Tak  | 18 grudnia, 2018  | Obniżanie poziomu do customappSSO |
| Żądania poprawek dla atrybutów wielowartościowych zawierają nieprawidłową składnię filtru ścieżki | Tak  |  18 grudnia, 2018  | Obniżanie poziomu do customappSSO |
| Żądania utworzenia grupy zawierają nieprawidłowy identyfikator URI schematu | Tak  |  18 grudnia, 2018  |  Obniżanie poziomu do customappSSO |
| Aktualizowanie zachowania poprawek w celu zapewnienia zgodności (np. aktywnego jako wartości logicznej i usuwania członkostwa w grupie) | Nie | TBD| Użyj flagi podglądu |

## <a name="flags-to-alter-the-scim-behavior"></a>Flagi zmieniające zachowanie standard scim
Użyj flag poniżej w adresie URL dzierżawy aplikacji, aby zmienić domyślne zachowanie klienta standard scim.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="Standard scim flagi do późniejszego zachowania.":::

* Użyj następującego adresu URL, aby zaktualizować zachowanie poprawek i zapewnić zgodność Standard scim (np. aktywna jako wartość logiczna i odpowiednie usunięcia członkostwa w grupie). To zachowanie jest obecnie dostępne tylko wtedy, gdy jest używana flaga, ale będzie zachowaniem domyślnym w ciągu następnych kilku miesięcy. Ta flaga podglądu nie działa obecnie z obsługą administracyjną na żądanie. 
  * **Adres URL (zgodny z standard scim):** AzureAdScimPatch062020
  * **Odwołania do Standard scim RFC:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **Domyślnie**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **Adres URL obniżenia poziomu:** Gdy nowe zachowanie zgodne z standard scim stanie się domyślne w aplikacji innej niż Galeria, można użyć następującego adresu URL, aby przywrócić stare, niezgodne zachowanie zgodności Standard scim: AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>Uaktualnianie ze starszego zadania customappsso do zadania Standard scim
Wykonanie poniższych kroków spowoduje usunięcie istniejącego zadania customappsso i utworzenie nowego zadania Standard scim. 
 
1. Zaloguj się do Azure Portal pod adresem https://portal.azure.com .
2. W sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw** Azure Portal Znajdź i wybierz istniejącą aplikację Standard scim.
3. W sekcji **Właściwości** istniejącej aplikacji Standard scim Skopiuj **Identyfikator obiektu**.
4. W nowym oknie przeglądarki sieci Web przejdź do https://developer.microsoft.com/graph/graph-explorer i zaloguj się jako administrator dla dzierżawy usługi Azure AD, w której aplikacja została dodana.
5. W Eksploratorze grafu uruchom poniższe polecenie, aby zlokalizować identyfikator zadania aprowizacji. Zastąp ciąg "[Object-ID]" IDENTYFIKATORem jednostki usługi (IDENTYFIKATORem obiektu) skopiowanym z trzeciego kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Pobierz zadania](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Pobierz zadania") 


6. W wynikach Skopiuj pełny ciąg "ID" zaczynający się od "customappsso" lub "Standard scim".
7. Uruchom poniższe polecenie, aby pobrać konfigurację mapowania atrybutów, aby można było utworzyć kopię zapasową. Użyj tego samego [Object-ID] jak poprzednio i Zastąp wartość [ID zadania] IDENTYFIKATORem zadania aprowizacji skopiowanego z ostatniego kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Pobierz schemat](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Pobierz schemat") 

8. Skopiuj dane wyjściowe JSON z ostatniego kroku i Zapisz je w pliku tekstowym. Plik JSON zawiera wszelkie niestandardowe mapowania atrybutów, które zostały dodane do starej aplikacji i powinny być w przybliżeniu kilka tysięcy wierszy w formacie JSON.
9. Uruchom poniższe polecenie, aby usunąć zadanie aprowizacji:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Uruchom poniższe polecenie, aby utworzyć nowe zadanie aprowizacji z najnowszymi poprawkami usługi.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. W wynikach ostatniego kroku Skopiuj pełny ciąg "ID" zaczynający się od "Standard scim". Opcjonalnie należy ponownie zastosować stare mapowania atrybutów, uruchamiając poniższe polecenie, zastępując wartość [New-Job-ID] nowym IDENTYFIKATORem zadania skopiowanego i wprowadzając dane wyjściowe JSON z kroku #7 jako treść żądania.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Wróć do pierwszego okna przeglądarki sieci Web i wybierz kartę **Inicjowanie obsługi** dla swojej aplikacji.
13. Sprawdź konfigurację, a następnie uruchom zadanie aprowizacji. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>Obniżenie wersji z zadania Standard scim do zadania customappsso (niezalecane)
 Pozwalamy na obniżenie wersji na stare zachowanie, ale nie zalecamy jej, ponieważ customappsso nie korzysta z niektórych aktualizacji, które wprowadziliśmy i mogą nie być obsługiwane w nieskończoność. 

1. Zaloguj się do Azure Portal pod adresem https://portal.azure.com .
2. w sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > Tworzenie aplikacji** dla Azure Portal, Utwórz nową aplikację w wersji **innej niż Galeria** .
3. W sekcji **Właściwości** nowej aplikacji NIESTANDARDOWEJ Skopiuj **Identyfikator obiektu**.
4. W nowym oknie przeglądarki sieci Web przejdź do https://developer.microsoft.com/graph/graph-explorer i zaloguj się jako administrator dla dzierżawy usługi Azure AD, w której aplikacja została dodana.
5. W Eksploratorze grafów uruchom poniższe polecenie, aby zainicjować konfigurację aprowizacji dla aplikacji.
   Zastąp ciąg "[Object-ID]" IDENTYFIKATORem jednostki usługi (IDENTYFIKATORem obiektu) skopiowanym z trzeciego kroku.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Wróć do pierwszego okna przeglądarki sieci Web i wybierz kartę **Inicjowanie obsługi** dla swojej aplikacji.
7. Ukończ konfigurację aprowizacji użytkowników, jak zwykle.


## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o aprowizacji i dezaktywowaniu aplikacji SaaS](user-provisioning.md)
