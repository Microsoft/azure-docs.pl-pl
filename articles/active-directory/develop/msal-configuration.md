---
title: Plik konfiguracji MSAL systemu Android | Azure
titleSuffix: Microsoft identity platform
description: Omówienie pliku konfiguracji biblioteki uwierzytelniania firmy Microsoft (MSAL) systemu Android, który reprezentuje konfigurację aplikacji w Azure Active Directory.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: aa0ce6a5f909e67f0551c8667bb7e5c5e6d7eb04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92275606"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Plik konfiguracji biblioteki uwierzytelniania firmy Microsoft dla systemu Android

Biblioteka Microsoft Authentication Library (MSAL) jest dostarczana z [domyślnym plikiem JSON konfiguracji](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) , który można dostosować w celu zdefiniowania zachowania publicznej aplikacji klienckiej dla takich elementów jak urząd domyślny, które urzędy będą używane i tak dalej.

Ten artykuł pomoże zrozumieć różne ustawienia w pliku konfiguracji oraz sposób określania pliku konfiguracji, który ma być używany w aplikacji opartej na MSAL.

## <a name="configuration-settings"></a>Ustawienia konfiguracji

### <a name="general-settings"></a>Ustawienia ogólne

| Właściwość | Typ danych | Wymagane | Uwagi |
|-----------|------------|-------------|-------|
| `client_id` | Ciąg | Tak | Identyfikator klienta aplikacji na [stronie rejestracji aplikacji](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Ciąg | Tak | Identyfikator URI przekierowania aplikacji ze [strony rejestracji aplikacji](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `broker_redirect_uri_registered` | Wartość logiczna | Nie | Możliwe wartości: `true` , `false` |
| `authorities` | Lista\<Authority> | Nie | Lista urzędów, których potrzebuje aplikacja |
| `authorization_user_agent` | AuthorizationAgent (enum) | Nie | Możliwe wartości: `DEFAULT` , `BROWSER` , `WEBVIEW` |
| `http` | HttpConfiguration | Nie | Skonfiguruj `HttpUrlConnection` `connect_timeout` i `read_timeout` |
| `logging` | LoggingConfiguration | Nie | Określa poziom szczegółowości rejestrowania. Konfiguracje opcjonalne obejmują: `pii_enabled` , która przyjmuje wartość logiczną, i `log_level` , która przyjmuje `ERROR` , `WARNING` , `INFO` lub `VERBOSE` . |

### <a name="client_id"></a>client_id

Identyfikator klienta lub identyfikator aplikacji, który został utworzony podczas rejestrowania aplikacji.

### <a name="redirect_uri"></a>redirect_uri

Identyfikator URI przekierowania zarejestrowany podczas rejestrowania aplikacji. Jeśli identyfikator URI przekierowania dotyczy aplikacji brokera, zapoznaj się z tematem [Identyfikator URI przekierowania dla publicznych aplikacji klienckich](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) , aby upewnić się, że używasz poprawnego formatu identyfikatora URI przekierowania dla aplikacji brokera.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Jeśli chcesz użyć uwierzytelniania obsługiwanego przez brokera, `broker_redirect_uri_registered` Właściwość musi być ustawiona na `true` . Jeśli w scenariuszu uwierzytelniania obsługiwanego przez brokera aplikacja nie ma poprawnego formatu do komunikowania się z brokerem, zgodnie z opisem w temacie [Identyfikator URI przekierowania dla publicznych aplikacji klienckich](msal-client-application-configuration.md#redirect-uri-for-public-client-apps), aplikacja sprawdza poprawność identyfikatora URI przekierowania i zgłasza wyjątek podczas uruchamiania.

### <a name="authorities"></a>wykazu

Lista znanych i zaufanych urzędów. Oprócz urzędów wymienionych w tym miejscu MSAL również wysyła zapytanie do firmy Microsoft w celu uzyskania listy chmur i urzędów znanych firmie Microsoft. Na tej liście urzędów Określ typ urzędu i wszelkie dodatkowe parametry opcjonalne, takie jak `"audience"` , które powinny być wyrównane z odbiorcami aplikacji w oparciu o rejestrację aplikacji. Poniżej przedstawiono przykładową listę urzędów:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenant_id": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Zamapuj urząd usługi AAD & odbiorców na punkty końcowe platformy tożsamości firmy Microsoft

| Typ | Grupy odbiorców | Identyfikator dzierżawy | Authority_Url | Wynikający punkt końcowy | Uwagi |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common` jest aliasem dzierżawy, w którym znajduje się konto. Takie jak określona dzierżawa Azure Active Directory lub system konto Microsoft. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Tylko konta obecne w contoso.com mogą uzyskać token. Każda zweryfikowana domena lub identyfikator GUID dzierżawy może być używany jako identyfikator dzierżawy. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | Tylko konta Azure Active Directory mogą być używane z tym punktem końcowym. Konta Microsoft mogą należeć do organizacji. Aby uzyskać token przy użyciu konto Microsoft dla zasobu w organizacji, określ dzierżawę organizacyjną, z której ma być używany token. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Tylko konta Microsoft mogą korzystać z tego punktu końcowego. |
| B2C | | | Zobacz otrzymany punkt końcowy | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Tylko konta znajdujące się w dzierżawie contoso.onmicrosoft.com mogą uzyskać token. W tym przykładzie zasady B2C są częścią ścieżki adresu URL urzędu certyfikacji. |

> [!NOTE]
> Nie można włączyć i wyłączyć walidacji urzędu w MSAL.
> Urzędy są znane jako deweloper określony przez konfigurację lub znany firmie Microsoft za pośrednictwem metadanych.
> Jeśli MSAL odbiera żądanie tokenu do nieznanego urzędu, `MsalClientException` typu `UnknownAuthority` wyników.
> Uwierzytelnianie obsługiwane przez brokera nie działa w przypadku Azure AD B2C.

#### <a name="authority-properties"></a>Właściwości urzędu

| Właściwość | Typ danych  | Wymagane | Uwagi |
|-----------|-------------|-----------|--------|
| `type` | Ciąg | Tak | Odzwierciedla grupę odbiorców lub konto, do którego należy aplikacja. Możliwe wartości: `AAD` , `B2C` |
| `audience` | Obiekt | Nie | Stosuje się tylko wtedy, gdy typ = `AAD` . Określa tożsamość, która jest przeznaczona dla aplikacji. Użyj wartości z rejestracji aplikacji |
| `authority_url` | Ciąg | Tak | Wymagane tylko wtedy, gdy typ = `B2C` . Określa adres URL urzędu lub zasady, które powinny być używane przez aplikację  |
| `default` | boolean | Tak | Jeden `"default":true` jest wymagany w przypadku określenia co najmniej jednego urzędu. |

#### <a name="audience-properties"></a>Właściwości odbiorców

| Właściwość | Typ danych  | Wymagane | Uwagi |
|-----------|-------------|------------|-------|
| `type` | Ciąg | Tak | Określa odbiorców, do których aplikacja chce kierować. Możliwe wartości: `AzureADandPersonalMicrosoftAccount` , `PersonalMicrosoftAccount` , `AzureADMultipleOrgs` , `AzureADMyOrg` |
| `tenant_id` | Ciąg | Tak | Wymagane tylko wtedy, gdy `"type":"AzureADMyOrg"` . Opcjonalne dla innych `type` wartości. Może to być domena dzierżawy, taka jak `contoso.com` lub identyfikator dzierżawy, taki jak `72f988bf-86f1-41af-91ab-2d7cd011db46` ). |

### <a name="authorization_user_agent"></a>authorization_user_agent

Wskazuje, czy podczas podpisywania konta lub autoryzowania dostępu do zasobu ma być używany osadzony widok WebView, czy domyślna przeglądarka na urządzeniu.

Możliwe wartości:
- `DEFAULT`: Preferuje przeglądarkę systemową. Program używa osadzonego widoku sieci Web, jeśli przeglądarka nie jest dostępna na urządzeniu.
- `WEBVIEW`: Użyj osadzonego widoku sieci Web.
- `BROWSER`: Używa domyślnej przeglądarki na urządzeniu.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

W przypadku klientów, którzy obsługują wiele chmur narodowych, określ `true` . Platforma tożsamości firmy Microsoft będzie następnie automatycznie przekierowywać do właściwej chmury krajowej podczas autoryzacji i wykupu. Aby określić chmurę krajową konta zalogowanego, można sprawdzić urząd skojarzony z `AuthenticationResult` . Należy pamiętać, że program `AuthenticationResult` nie udostępnia krajowego adresu punktu końcowego dla zasobu, dla którego zażądano tokenu.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Wartość logiczna wskazująca, czy używasz usługi Microsoft Identity Broker zgodnej z identyfikatorem URI przekierowania brokera. Ustaw na, `false` Jeśli nie chcesz używać brokera w aplikacji.

Jeśli używasz urzędu usługi AAD z zestawem odbiorców ustawionym na `"MicrosoftPersonalAccount"` , Broker nie będzie używany.

### <a name="http"></a>http

Skonfiguruj ustawienia globalne limitów czasu HTTP, takie jak:

| Właściwość | Typ danych | Wymagane | Uwagi |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Nie | Czas w milisekundach |
| `read_timeout` | int | Nie | Czas w milisekundach |

### <a name="logging"></a>rejestrowanie

Następujące ustawienia globalne dotyczą rejestrowania:

| Właściwość | Typ danych  | Wymagane | Uwagi |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | Nie | Czy emitować dane osobowe |
| `log_level`   | ciąg | Nie | Komunikaty dziennika do danych wyjściowych. Obsługiwane poziomy dziennika obejmują `ERROR` , `WARNING` , `INFO` , i `VERBOSE` . |
| `logcat_enabled` | boolean | Nie | Określa, czy oprócz interfejsu rejestrowania ma być wyprowadzane wyjście do dziennika Cat |

### <a name="account_mode"></a>account_mode

Określa liczbę kont, które mogą być używane w aplikacji w danym momencie. Możliwe wartości są następujące:

- `MULTIPLE` Wartooć
- `SINGLE`

Konstruowanie `PublicClientApplication` przy użyciu trybu konta, który nie jest zgodny z tym ustawieniem, spowoduje wyjątek.

Aby uzyskać więcej informacji o różnicach między pojedynczym i wieloma kontami, zobacz [pojedyncze i wiele aplikacji konta](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

Lista dozwolonych przeglądarek, które są zgodne z MSAL. Te przeglądarki poprawnie obsługują przekierowania do niestandardowych intencji. Możesz dodać do tej listy. Domyślna konfiguracja jest pokazana poniżej.
``
## <a name="the-default-msal-configuration-file"></a>Domyślny plik konfiguracji MSAL

Poniżej przedstawiono domyślną konfigurację MSAL, która jest dostarczana z MSAL. Najnowszą wersję można zobaczyć w witrynie [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Ta konfiguracja jest uzupełniona o wartości, które zapewniasz. Wartości, które zapewniasz, zastąpią ustawienia domyślne.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Przykładowa konfiguracja podstawowa

Poniższy przykład ilustruje podstawową konfigurację, która określa identyfikator klienta, identyfikator URI przekierowania, to czy przekierowanie brokera jest zarejestrowane oraz listę urzędów.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Jak używać pliku konfiguracji

1. Utwórz plik konfiguracji. Zalecamy utworzenie niestandardowego pliku konfiguracji w programie `res/raw/auth_config.json` . Ale możesz ją umieścić w dowolnym miejscu.
2. Poinformuj MSAL o tym, gdzie należy wyszukać konfigurację podczas konstruowania `PublicClientApplication` . Na przykład:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
