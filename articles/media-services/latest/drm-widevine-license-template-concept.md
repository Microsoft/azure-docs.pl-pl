---
title: Przegląd szablonu licencji usługi Media Services v3 Widevine
description: Dowiedz się więcej na temat Azure Media Services za pomocą szablonu licencji Widevine i sposobu jego użycia w celu skonfigurowania licencji Widevine.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 098421239549f1ab8418ced03de14c11dae84065
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069153"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Omówienie szablonu licencji Media Services v3 with Widevine

Azure Media Services umożliwia zaszyfrowanie zawartości za pomocą usługi **Google Widevine**. Media Services udostępnia również usługę do dostarczania licencji Widevine. Za pomocą Azure Media Services interfejsów API można konfigurować licencje Widevine. Gdy gracz podejmie próbę odtworzenia zawartości chronionej przez Widevine, żądanie jest wysyłane do usługi dostarczania licencji w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, usługa wystawia licencję. Jest on wysyłany do klienta i jest używany do odszyfrowywania i odtwarzania określonej zawartości.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Żądanie licencji Widevine jest sformatowane jako komunikat JSON.  



```json
{  
    "payload":"<license challenge>",
    "content_id": "<content id>"
    "provider": "<provider>"
    "allowed_track_types":"<types>",
    "content_key_specs":[  
        {  
            "track_type":"<track type 1>"
        },
        {  
            "track_type":"<track type 2>"
        },
        …
    ],
    "policy_overrides":{  
        "can_play":<can play>,
        "can persist":<can persist>,
        "can_renew":<can renew>,
        "rental_duration_seconds":<rental duration>,
        "playback_duration_seconds":<playback duration>,
        "license_duration_seconds":<license duration>,
        "renewal_recovery_duration_seconds":<renewal recovery duration>,
        "renewal_server_url":"<renewal server url>",
        "renewal_delay_seconds":<renewal delay>,
        "renewal_retry_interval_seconds":<renewal retry interval>,
        "renew_with_usage":<renew with usage>
    }
}
```

>[!NOTE]
> Można utworzyć pusty komunikat bez wartości, tylko " {} ." Następnie tworzony jest szablon licencji z wartościami domyślnymi. Wartość domyślna działa w większości przypadków. W przypadku scenariuszy dostarczania licencji na podstawie firmy Microsoft należy zawsze używać wartości domyślnych. Jeśli musisz ustawić wartości "Provider" i "content_id", dostawca musi być zgodny z poświadczeniami Widevine.

## <a name="json-message"></a>Komunikat JSON

| Nazwa | Wartość | Opis |
| --- | --- | --- |
| payload |Ciąg zakodowany algorytmem Base64 |Żądanie licencji wysyłane przez klienta. |
| content_id |Ciąg zakodowany algorytmem Base64 |Identyfikator używany do wygenerowania identyfikatora klucza i klucza zawartości dla każdego content_key_specs. track_type. |
| dostawcy |ciąg |Służy do wyszukiwania kluczy zawartości i zasad. Jeśli do dostawy licencji Widevine jest używany program Microsoft Key Delivery, ten parametr jest ignorowany. |
| policy_name |ciąg |Nazwa wcześniej zarejestrowanych zasad. Opcjonalny. |
| allowed_track_types |enum |SD_ONLY lub SD_HD. Kontroluje, które klucze zawartości znajdują się w licencji. |
| content_key_specs |Tablica struktur JSON, zobacz sekcję "specyfikacje klucza zawartości".  |Dokładniejsza kontrolka, na której klucze zawartości mają być zwracane. Aby uzyskać więcej informacji, zobacz sekcję "specyfikacje klucza zawartości". Można określić tylko jedną z wartości allowed_track_types i content_key_specs. |
| use_policy_overrides_exclusively |Wartość logiczna, true lub false |Użyj atrybutów zasad określonych przez policy_overrides i Pomiń wszystkie poprzednio przechowywane zasady. |
| policy_overrides |Strukturę JSON, zobacz sekcję "zastąpienia zasad". |Ustawienia zasad dla tej licencji.  W przypadku, gdy ten zasób ma wstępnie zdefiniowane zasady, są używane te określone wartości. |
| session_init |Strukturę JSON, zobacz sekcję "Inicjowanie sesji". |Do licencji są przesyłane opcjonalne dane. |
| parse_only |Wartość logiczna, true lub false |Żądanie licencji jest analizowane, ale nie wydano licencji. Jednak wartości z żądania licencji są zwracane w odpowiedzi. |

## <a name="content-key-specs"></a>Specyfikacje klucza zawartości
Jeśli istnieją już istniejące zasady, nie ma potrzeby określania żadnych wartości w specyfikacji klucza zawartości. Istniejące wcześniej zasady skojarzone z tą zawartością są używane do określania ochrony danych wyjściowych, takich jak Digital Content Protection (HDCP) i kopia ogólnego systemu zarządzania (CGMS). Jeśli wstępnie istniejące zasady nie są zarejestrowane na serwerze licencji Widevine, dostawca zawartości może wstrzyknąć wartości do żądania licencji.   

Każda wartość content_key_specs musi być określona dla wszystkich ścieżek, niezależnie od opcji use_policy_overrides_exclusively. 

| Nazwa | Wartość | Opis |
| --- | --- | --- |
| content_key_specs. track_type |ciąg |Nazwa typu ścieżki. Jeśli w żądaniu licencji określono content_key_specs, upewnij się, że wszystkie typy śledzenia są jawnie określone. Niewykonanie tej czynności spowoduje niepowodzenie odtwarzania ostatnich 10 sekund. |
| content_key_specs  <br/> security_level |równ |Definiuje wymagania dotyczące niezawodności klientów na potrzeby odtwarzania. <br/> — Wymagana jest Kryptografia białych pól oparta na oprogramowaniu. <br/> — Wymagane są Kryptografia programowa i niesłonięty dekoder. <br/> — Materiał klucza i operacje kryptografii muszą być wykonywane w ramach sprzętowego środowiska wykonawczego, które jest w użyciu. <br/> — Kryptografia i dekodowanie zawartości należy wykonać w ramach sprzętowego środowiska wykonawczego, które jest wykonywane w ramach sprzętu.  <br/> — Kryptografia, dekodowanie i wszystkie czynności związane z multimediami (skompresowane i nieskompresowane) muszą być obsługiwane w ramach sprzętowego środowiska wykonawczego. |
| content_key_specs <br/> required_output_protection. używający HDC |ciąg, jeden z HDCP_NONE, HDCP_V1, HDCP_V2 |Wskazuje, czy jest wymagana HDCP. |
| content_key_specs <br/>key |Zakodowan<br/>zakodowany ciąg |Klucz zawartości, który ma być używany dla tej ścieżki. Jeśli ta wartość jest określona, wymagane jest track_type lub key_id. Dostawca zawartości może użyć tej opcji, aby wstrzyknąć klucz zawartości dla tej ścieżki zamiast zezwalać serwerowi licencji Widevine na generowanie lub Wyszukiwanie klucza. |
| content_key_specs content_key_specs.Key_ID |Binarne ciągi zakodowane algorytmem Base64, 16 bajtów |Unikatowy identyfikator klucza. |

## <a name="policy-overrides"></a>Zastąpienia zasad
| Nazwa | Wartość | Opis |
| --- | --- | --- |
| policy_overrides&#46;can_play |Wartość logiczna, true lub false |Wskazuje, że odtwarzanie zawartości jest dozwolone. Wartość domyślna to false. |
| policy_overrides&#46;can_persist |Wartość logiczna, true lub false |Wskazuje, że licencja na magazyn nietrwały do użycia w trybie offline może zostać utrwalona. Wartość domyślna to false. |
| policy_overrides&#46;can_renew |Wartość logiczna, true lub false |Wskazuje, że odnowienie tej licencji jest dozwolone. W przypadku wartości true czas trwania licencji może zostać rozszerzony przez puls. Wartość domyślna to false. |
| policy_overrides&#46;license_duration_seconds |Int64 |Wskazuje przedział czasu dla tej konkretnej licencji. Wartość 0 oznacza, że nie ma limitu czasu trwania. Wartość domyślna to 0. |
| policy_overrides&#46;rental_duration_seconds |Int64 |Wskazuje przedział czasu, w którym jest dozwolone odtwarzanie. Wartość 0 oznacza, że nie ma limitu czasu trwania. Wartość domyślna to 0. |
| policy_overrides&#46;playback_duration_seconds |Int64 |Okno wyświetlania czasu po zakończeniu odtwarzania rozpocznie się w okresie obowiązywania licencji. Wartość 0 oznacza, że nie ma limitu czasu trwania. Wartość domyślna to 0. |
| policy_overrides&#46;renewal_server_url |ciąg |Wszystkie żądania pulsu (odnowienie) dla tej licencji są kierowane do określonego adresu URL. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides&#46;renewal_delay_seconds |Int64 |Liczba sekund przed pierwszym odnowieniem license_start_time. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. Wartość domyślna to 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |Int64 |Określa opóźnienie (w sekundach) między kolejnymi żądaniami odnowienia licencji, w przypadku awarii. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides&#46;renewal_recovery_duration_seconds |Int64 |Okno czasu, w którym odtwarzanie może być kontynuowane podczas próby odnowienia, ale nie powiodło się z powodu problemów zaplecza z serwerem licencji. Wartość 0 oznacza, że nie ma limitu czasu trwania. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides&#46;renew_with_usage |Wartość logiczna, true lub false |Wskazuje, że licencja jest wysyłana do odnowienia, gdy zostanie uruchomione użycie. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |

## <a name="session-initialization"></a>Inicjowanie sesji
| Nazwa | Wartość | Opis |
| --- | --- | --- |
| provider_session_token |Ciąg zakodowany algorytmem Base64 |Ten token sesji jest przenoszona z powrotem do licencji i istnieje w kolejnych odnowieniach. Token sesji nie utrzymuje się poza sesjami. |
| provider_client_token |Ciąg zakodowany algorytmem Base64 |Token klienta umożliwiający ponowne wysłanie odpowiedzi na licencję. Jeśli żądanie licencji zawiera token klienta, ta wartość jest ignorowana. Token klienta utrzymuje się poza sesjami licencji. |
| override_provider_client_token |Wartość logiczna, true lub false |Jeśli wartość jest równa false, a żądanie licencji zawiera token klienta, użyj tokenu z żądania, nawet jeśli token klienta został określony w tej strukturze. W przypadku wartości true należy zawsze używać tokenu określonego w tej strukturze. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurowanie licencji Widevine przy użyciu platformy .NET 

Media Services udostępnia klasę, która umożliwia skonfigurowanie licencji Widevine. Aby utworzyć licencję, Przekaż kod JSON do [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Aby skonfigurować szablon, można:

### <a name="directly-construct-a-json-string"></a>Bezpośrednie konstruowanie ciągu JSON

Ta metoda może być podatna na błędy. Zaleca się użycie innej metody, opisanej w temacie [Definiowanie wymaganych klas i Serializacja do JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a> Zdefiniuj wymaganą klasę i serializować do formatu JSON

#### <a name="define-classes"></a>Definiuj klasy

W poniższym przykładzie przedstawiono przykład definicji klas, które są mapowane na schemat JSON Widevine. Można utworzyć wystąpienia klas przed ich serializacji do ciągu JSON.  

```csharp
/// <summary>
/// Widevine PolicyOverrides class.
/// </summary>
public class PolicyOverrides
{
    /// <summary>
    /// Gets or sets a value indicating whether playback of the content is allowed. Default is false.
    /// </summary>
    [JsonProperty("can_play")]
    public bool CanPlay { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether the license might be persisted to nonvolatile storage for offline use. Default is false.
    /// </summary>
    [JsonProperty("can_persist")]
    public bool CanPersist { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether renewal of this license is allowed. If true, the duration of the license can be extended by heartbeat. Default is false.
    /// </summary>
    [JsonProperty("can_renew")]
    public bool CanRenew { get; set; }

    /// <summary>
    /// Gets or sets the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("rental_duration_seconds")]
    public int RentalDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("playback_duration_seconds")]
    public int PlaybackDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("license_duration_seconds")]
    public int LicenseDurationSeconds { get; set; }
}

/// <summary>
/// Widevine ContentKeySpec class.
/// </summary>
public class ContentKeySpec
{
    /// <summary>
    /// Gets or sets track type.
    /// If content_key_specs is specified in the license request, make sure to specify all track types explicitly.
    /// Failure to do so results in failure to play back past 10 seconds.
    /// </summary>
    [JsonProperty("track_type")]
    public string TrackType { get; set; }

    /// <summary>
    /// Gets or sets client robustness requirements for playback.
    /// Software-based white-box cryptography is required.
    /// Software cryptography and an obfuscated decoder are required.
    /// The key material and cryptography operations must be performed within a hardware-backed trusted execution environment.
    /// The cryptography and decoding of content must be performed within a hardware-backed trusted execution environment.
    /// The cryptography, decoding, and all handling of the media (compressed and uncompressed) must be handled within a hardware-backed trusted execution environment.
    /// </summary>
    [JsonProperty("security_level")]
    public int SecurityLevel { get; set; }

    /// <summary>
    /// Gets or sets the OutputProtection.
    /// </summary>
    [JsonProperty("required_output_protection")]
    public OutputProtection RequiredOutputProtection { get; set; }
}

/// <summary>
/// OutputProtection Widevine class.
/// </summary>
public class OutputProtection
{
    /// <summary>
    /// Gets or sets HDCP protection.
    /// Supported values : HDCP_NONE, HDCP_V1, HDCP_V2
    /// </summary>
    [JsonProperty("hdcp")]
    public string HDCP { get; set; }

    /// <summary>
    /// Gets or sets CGMS.
    /// </summary>
    [JsonProperty("cgms_flags")]
    public string CgmsFlags { get; set; }
}

/// <summary>
/// Widevine template.
/// </summary>
public class WidevineTemplate
{
    /// <summary>
    /// Gets or sets the allowed track types.
    /// SD_ONLY or SD_HD.
    /// Controls which content keys are included in a license.
    /// </summary>
    [JsonProperty("allowed_track_types")]
    public string AllowedTrackTypes { get; set; }

    /// <summary>
    /// Gets or sets a finer-grained control on which content keys to return.
    /// For more information, see the section "Content key specs."
    /// Only one of the allowed_track_types and content_key_specs values can be specified.
    /// </summary>
    [JsonProperty("content_key_specs")]
    public ContentKeySpec[] ContentKeySpecs { get; set; }

    /// <summary>
    /// Gets or sets policy settings for the license.
    /// In the event this asset has a predefined policy, these specified values are used.
    /// </summary>
    [JsonProperty("policy_overrides")]
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Skonfiguruj licencję

Użyj klas zdefiniowanych w poprzedniej sekcji, aby utworzyć kod JSON, który jest używany do konfigurowania [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tematem [Ochrona przy użyciu technologii DRM](drm-protect-with-drm-tutorial.md)
