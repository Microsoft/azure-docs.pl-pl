---
title: Szablony stron na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak dostosować zawartość szablonów stron portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 24d026785025dba4ae45de404edec67c2cf3871a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91335589"
---
# <a name="page-templates-in-azure-api-management"></a>Szablony stron na platformie Azure API Management
Usługa Azure API Management umożliwia dostosowanie zawartości stron portalu dla deweloperów przy użyciu zestawu szablonów, które konfigurują ich zawartość. Korzystając z składni [DotLiquid](http://dotliquidmarkup.org/) i wybranego edytora, takiego jak [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), i dostępnego zestawu zlokalizowanych [zasobów ciągów](api-management-template-resources.md#strings), [zasobów glifów](api-management-template-resources.md#glyphs)i [kontrolek stron](api-management-page-controls.md), masz doskonałą elastyczność konfigurowania zawartości stron w miarę, w jakiej są one widoczne przy użyciu tych szablonów.  
  
 Szablony w tej sekcji umożliwiają dostosowanie zawartości stron logowania, rejestracji i nieznalezienia stron w portalu dla deweloperów.  
  
-   [Zaloguj się](#SignIn)  
  
-   [Rejestrowanie](#SignUp)  
  
-   [Nie znaleziono strony](#PageNotFound)  
  
> [!NOTE]
>  Przykładowe szablony domyślne są zawarte w poniższej dokumentacji, ale mogą ulec zmianie ze względu na ciągłe ulepszenia. Możesz wyświetlić szablony domyślne na żywo w portalu dla deweloperów, przechodząc do żądanych poszczególnych szablonów. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="sign-in"></a><a name="SignIn"></a> Rejestrowanie  
 Szablon **logowania** umożliwia dostosowanie strony logowania w portalu dla deweloperów.  
  
 ![Strona logowania](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM — szablony portalu dla deweloperów")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Formanty  
 Ten szablon może korzystać z następujących [kontrolek strony](api-management-page-controls.md).  
  
-   [Logowanie Podstawowe](api-management-page-controls.md#basic-signin)  
  
-   [udostępnia](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Model danych  
 Jednostka [logowania użytkownika](api-management-template-data-model-reference.md#UseSignIn) .  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="sign-up"></a><a name="SignUp"></a> Zarejestruj się  
 Szablon **rejestracji** umożliwia dostosowanie strony rejestracji w portalu dla deweloperów.  
  
 ![Strona rejestracji](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM rejestracji — szablony portalu dla deweloperów")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Formanty  
 Ten szablon może korzystać z następujących [kontrolek strony](api-management-page-controls.md).  
  
-   [Utwórz konto](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Model danych  
 Jednostka [rejestracji użytkownika](api-management-template-data-model-reference.md#UserSignUp) .  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="page-not-found"></a><a name="PageNotFound"></a> Nie znaleziono strony  
 Szablon **nie znaleziono strony** pozwala na dostosowanie strony, która nie została znaleziona w portalu dla deweloperów.  
  
 ![Strona nie została znaleziona](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "Nie znaleziono APIM szablonów portalu dla deweloperów")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Formanty  
 Ten szablon nie może używać żadnych [kontrolek strony](api-management-page-controls.md).  
  
### <a name="data-model"></a>Model danych  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|referenceCode|ciąg|Kod wygenerowany, jeśli ta strona była wyświetlana jako wynik błędu wewnętrznego.|  
|errorCode|ciąg|Kod wygenerowany, jeśli ta strona była wyświetlana jako wynik błędu wewnętrznego.|  
|emailBody|ciąg|Treść wiadomości e-mail wygenerowanej, jeśli ta strona była wyświetlana jako wynik błędu wewnętrznego.|  
|requestedUrl|ciąg|Adres URL żądany, gdy strona nie została znaleziona.|  
|referrerUrl|ciąg|Adres URL odwołującego się do żądanego adresu URL.|  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
