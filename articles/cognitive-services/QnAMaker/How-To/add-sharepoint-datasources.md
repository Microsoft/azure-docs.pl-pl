---
title: Pliki programu SharePoint — QnA Maker
description: Dodaj zabezpieczone źródła danych programu SharePoint do bazy wiedzy, aby wzbogacić bazę wiedzy z pytaniami i odpowiedziami, które mogą być zabezpieczone za pomocą Active Directory.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 0f49621d44b99412b9a6ab0360c30c894a71e5d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776871"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Dodawanie bezpiecznego źródła danych programu SharePoint do bazy wiedzy

Dodaj zabezpieczone źródła danych programu SharePoint oparte na chmurze do bazy wiedzy, aby wzbogacić bazę wiedzy z pytaniami i odpowiedziami, które mogą być zabezpieczone za pomocą Active Directory.

Po dodaniu bezpiecznego dokumentu programu SharePoint do bazy wiedzy jako Menedżera QnA Maker należy zażądać uprawnień Active Directory do QnA Maker. Po otrzymaniu tego uprawnienia z Menedżera Active Directory, aby QnA Maker dostęp do programu SharePoint, nie trzeba go ponownie pomieścić. Każdy kolejny dokument dodany do bazy wiedzy nie będzie wymagał autoryzacji, jeśli znajduje się w tym samym zasobie programu SharePoint.

Jeśli Menedżer bazy wiedzy QnA Maker nie jest menedżerem Active Directory, należy skontaktować się z menedżerem Active Directory, aby zakończyć ten proces.

## <a name="prerequisites"></a>Wymagania wstępne

* Oparta na chmurze QnA Maker SharePoint używa Microsoft Graph w celu uzyskania uprawnień. Jeśli program SharePoint działa lokalnie, nie będzie można wyodrębnić z programu SharePoint, ponieważ Microsoft Graph nie będzie można określić uprawnień.
* Format adresu URL — QnA Maker obsługuje tylko adresy URL programu SharePoint, które są generowane na potrzeby udostępniania i mają format `https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Dodaj obsługiwane typy plików do bazy wiedzy

Do bazy wiedzy można dodać wszystkie [typy plików](../Concepts/content-types.md) obsługiwane przez QNA Maker z witryny programu SharePoint. Może być konieczne przyznanie [uprawnień](#permissions) , jeśli zasób pliku jest zabezpieczony.

1. Z biblioteki z witryną programu SharePoint wybierz menu wielokropka pliku `...` .
1. Skopiuj adres URL pliku.

   ![Pobierz adres URL pliku programu SharePoint, wybierając menu wielokropka pliku, a następnie kopiując adres URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Na stronie **Ustawienia** w portalu QNA Maker Dodaj adres URL do bazy wiedzy.

### <a name="images-with-sharepoint-files"></a>Obrazy z plikami programu SharePoint

Jeśli pliki zawierają obrazy, nie są wyodrębniane. Możesz dodać obraz z portalu QnA Maker po wyodrębnieniu pliku do par QnA.

Dodaj obraz z następującą składnią promocji:

```markdown
![Explanation or description of image](URL of public image)
```

Tekst w nawiasach kwadratowych, `[]` objaśnia obraz. Adres URL w nawiasach, `()` jest bezpośrednim linkiem do obrazu.

Po przetestowaniu pary QnA w panelu test interaktywny w portalu QnA Maker zostanie wyświetlony obraz zamiast tekstu z promocji. Spowoduje to zweryfikowanie, że obraz można pobrać publicznie z aplikacji klienckiej.

## <a name="permissions"></a>Uprawnienia

Udzielanie uprawnień występuje, gdy zabezpieczony plik z programu SharePoint Server zostanie dodany do bazy wiedzy. W zależności od konfiguracji programu SharePoint i uprawnień osoby, która dodaje plik, może to wymagać:

* Brak dodatkowych kroków — osoba, która doda plik, ma wszystkie wymagane uprawnienia.
* kroki wykonywane przez [Menedżera bazy wiedzy](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) i [Menedżera Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Zapoznaj się z poniższymi instrukcjami.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Menedżer bazy wiedzy: Dodawanie źródła danych programu SharePoint w portalu QnA Maker

Gdy **menedżer QNA Maker** dodaje zabezpieczony dokument programu SharePoint do bazy wiedzy, Menedżer bazy wiedzy inicjuje żądanie dotyczące uprawnień wymaganych przez Menedżera Active Directory.

Żądanie rozpoczyna się od wyskakującego okienka w celu uwierzytelnienia na koncie Active Directory.

![Uwierzytelnianie konta użytkownika](../media/add-sharepoint-datasources/authenticate-user-account.png)

Po wybraniu konta przez Menedżera QnA Maker administrator Azure Active Directory otrzyma powiadomienie, że musi zezwolić na dostęp do aplikacji QnA Maker (nie Menedżera QnA Maker) do zasobu programu SharePoint. Menedżer Azure Active Directory będzie musiał wykonać tę czynność dla każdego zasobu programu SharePoint, ale nie do każdego dokumentu w tym zasobie.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Menedżer usługi Active Directory: udzielanie dostępu do odczytu pliku do QnA Maker

Menedżer Active Directory (nie Menedżer QnA Maker) musi udzielić dostępu do QnA Maker dostępu do zasobu programu SharePoint przez wybranie [tego linku](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) w celu autoryzowania aplikacji Portal QNA Maker SharePoint Enterprise, aby mieć uprawnienia do odczytu pliku.

![Azure Active Directory Manager przyznaje uprawnienia interaktywnie](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Udzielanie dostępu z poziomu Centrum administracyjnego Azure Active Directory

1. Menedżer Active Directory loguje się do Azure Portal i otwiera **[aplikacje dla przedsiębiorstw](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**.

1. Wyszukaj `QnAMakerPortalSharePoint` pozycję Wybierz aplikację QNA Maker.

    [![Wyszukaj QnAMakerPortalSharePoint na liście aplikacji dla przedsiębiorstw](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. W obszarze **zabezpieczenia**przejdź do pozycji **uprawnienia**. Wybierz pozycję **Udziel zgody administratora na organizację**.

    [![Wybierz uwierzytelnionego użytkownika dla administratora Active Directory](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Wybierz konto Sign-On z uprawnieniami do przyznawania uprawnień dla Active Directory.



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Współpracuj nad bazą wiedzy](collaborate-knowledge-base.md)
