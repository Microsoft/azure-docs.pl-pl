---
title: Przegląd szablonu licencji Widevine | Microsoft Docs
description: Ten temat zawiera omówienie szablonu licencji Widevine, który służy do konfigurowania licencji Widevine.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4a3805e50ce48d1439696a3d9015d6ba8571470
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011191"
---
# <a name="widevine-license-template-overview"></a>Omówienie szablonu licencji Widevine

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Za pomocą Azure Media Services można konfigurować i żądać licencji usługi Google Widevine. Gdy gracz podejmie próbę odtworzenia zawartości chronionej przez Widevine, żądanie jest wysyłane do usługi dostarczania licencji w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, usługa wystawia licencję. Jest on wysyłany do klienta i jest używany do odszyfrowywania i odtwarzania określonej zawartości.

Żądanie licencji Widevine jest sformatowane jako komunikat JSON.  

>[!NOTE]
> Można utworzyć pusty komunikat bez wartości, tylko " {} ." Następnie tworzony jest szablon licencji z wartościami domyślnymi. Wartość domyślna działa w większości przypadków. W przypadku scenariuszy dostarczania licencji na podstawie firmy Microsoft należy zawsze używać wartości domyślnych. Jeśli musisz ustawić wartości "Provider" i "content_id", dostawca musi być zgodny z poświadczeniami Widevine.

```json
{  
   "payload": "<license challenge>",
   "content_id": "<content id>" 
   "provider": "<provider>"
   "allowed_track_types": "<types>",
   "content_key_specs": [  
      {  
         "track_type": "<track type 1>"
      },
      {  
         "track_type": "<track type 2>"
      },
      …
   ],
   "policy_overrides": {  
      "can_play": <can play>,
      "can persist": <can persist>,
      "can_renew": <can renew>,
      "rental_duration_seconds": <rental duration>,
      "playback_duration_seconds": <playback duration>,
      "license_duration_seconds": <license duration>,
      "renewal_recovery_duration_seconds": <renewal recovery duration>,
      "renewal_server_url": "<renewal server url>",
      "renewal_delay_seconds": <renewal delay>,
      "renewal_retry_interval_seconds": <renewal retry interval>,
      "renew_with_usage": <renew with usage>
   }
}
```

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
Jeśli istnieją już istniejące zasady, nie ma potrzeby określania żadnych wartości w specyfikacji klucza zawartości. Istniejące zasady skojarzone z tą zawartością są używane do określania ochrony danych wyjściowych, takich jak Digital Content Protection (HDCP) i Copy General Management System (CGMS). Jeśli istniejące zasady nie są zarejestrowane na serwerze licencji Widevine, dostawca zawartości może wstrzyknąć wartości do żądania licencji.   

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
| policy_overrides. can_play |Wartość logiczna, true lub false |Wskazuje, że odtwarzanie zawartości jest dozwolone. Wartość domyślna to false. |
| policy_overrides. can_persist |Wartość logiczna, true lub false |Wskazuje, że licencja na magazyn nietrwały do użycia w trybie offline może zostać utrwalona. Wartość domyślna to false. |
| policy_overrides. can_renew |Wartość logiczna, true lub false |Wskazuje, że odnowienie tej licencji jest dozwolone. W przypadku wartości true czas trwania licencji może zostać rozszerzony przez puls. Wartość domyślna to false. |
| policy_overrides. license_duration_seconds |Int64 |Wskazuje przedział czasu dla tej konkretnej licencji. Wartość 0 oznacza, że nie ma limitu czasu trwania. Wartość domyślna to 0. |
| policy_overrides. rental_duration_seconds |Int64 |Wskazuje przedział czasu, w którym jest dozwolone odtwarzanie. Wartość 0 oznacza, że nie ma limitu czasu trwania. Wartość domyślna to 0. |
| policy_overrides. playback_duration_seconds |Int64 |Okno wyświetlania czasu po zakończeniu odtwarzania rozpocznie się w okresie obowiązywania licencji. Wartość 0 oznacza, że nie ma limitu czasu trwania. Wartość domyślna to 0. |
| policy_overrides. renewal_server_url |ciąg |Wszystkie żądania pulsu (odnowienie) dla tej licencji są kierowane do określonego adresu URL. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides. renewal_delay_seconds |Int64 |Liczba sekund przed pierwszym odnowieniem license_start_time. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. Wartość domyślna to 0. |
| policy_overrides. renewal_retry_interval_seconds |Int64 |Określa opóźnienie (w sekundach) między kolejnymi żądaniami odnowienia licencji, w przypadku awarii. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |Okno czasu, w którym odtwarzanie może być kontynuowane podczas próby odnowienia, ale nie powiodło się z powodu problemów zaplecza z serwerem licencji. Wartość 0 oznacza, że nie ma limitu czasu trwania. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |
| policy_overrides. renew_with_usage |Wartość logiczna, true lub false |Wskazuje, że licencja jest wysyłana do odnowienia, gdy zostanie uruchomione użycie. To pole jest używane tylko wtedy, gdy can_renew ma wartość true. |

## <a name="session-initialization"></a>Inicjowanie sesji
| Nazwa | Wartość | Opis |
| --- | --- | --- |
| provider_session_token |Ciąg zakodowany algorytmem Base64 |Ten token sesji jest przenoszona z powrotem do licencji i istnieje w kolejnych odnowieniach. Token sesji nie utrzymuje się poza sesjami. |
| provider_client_token |Ciąg zakodowany algorytmem Base64 |Token klienta umożliwiający ponowne wysłanie odpowiedzi na licencję. Jeśli żądanie licencji zawiera token klienta, ta wartość jest ignorowana. Token klienta utrzymuje się poza sesjami licencji. |
| override_provider_client_token |Wartość logiczna, true lub false |Jeśli wartość jest równa false, a żądanie licencji zawiera token klienta, użyj tokenu z żądania, nawet jeśli token klienta został określony w tej strukturze. W przypadku wartości true należy zawsze używać tokenu określonego w tej strukturze. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Konfigurowanie licencji Widevine przy użyciu typów .NET
Media Services udostępnia interfejsy API platformy .NET, których można użyć do skonfigurowania licencji Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Klasy zdefiniowane w Media Services .NET SDK
Następujące klasy są definicjami tych typów:

```dotnetcli
public class WidevineMessage
{
    public WidevineMessage();

    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public AllowedTrackTypes? allowed_track_types { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public ContentKeySpecs[] content_key_specs { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public object policy_overrides { get; set; }
}

[JsonConverter(typeof(StringEnumConverter))]
public enum AllowedTrackTypes
{
    SD_ONLY = 0,
    SD_HD = 1
}
public class ContentKeySpecs
{
    public ContentKeySpecs();

    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public string key_id { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public RequiredOutputProtection required_output_protection { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public int? security_level { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public string track_type { get; set; }
}

public class RequiredOutputProtection
{
    public RequiredOutputProtection();

    public Hdcp hdcp { get; set; }
}

[JsonConverter(typeof(StringEnumConverter))]
public enum Hdcp
{
    HDCP_NONE = 0,
    HDCP_V1 = 1,
    HDCP_V2 = 2
}
```

### <a name="example"></a>Przykład
W poniższym przykładzie pokazano, jak skonfigurować prostą licencję Widevine przy użyciu interfejsów API platformy .NET:

```dotnetcli
private static string ConfigureWidevineLicenseTemplate()
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            can_renew = false
        }
    };

    string configuration = JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Używanie dynamicznego szyfrowania Common Encryption w usługach PlayReady i Widevine](media-services-protect-with-playready-widevine.md)

