---
title: Omówienie szablonu licencji Azure Media Services v3 with Widevine
description: Ten temat zawiera omówienie szablonu licencji Widevine, który służy do konfigurowania licencji Widevine.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 94ce5e45a9a43e81020096ddc0a67429b286d9b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76705636"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Omówienie szablonu licencji Media Services v3 with Widevine

Azure Media Services umożliwia zaszyfrowanie zawartości za pomocą usługi **Google Widevine**. Media Services udostępnia również usługę do dostarczania licencji Widevine. Za pomocą Azure Media Services interfejsów API można konfigurować licencje Widevine. Gdy gracz podejmie próbę odtworzenia zawartości chronionej przez Widevine, żądanie jest wysyłane do usługi dostarczania licencji w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, usługa wystawia licencję. Jest on wysyłany do klienta i jest używany do odszyfrowywania i odtwarzania określonej zawartości.

Żądanie licencji Widevine jest sformatowane jako komunikat JSON.  

>[!NOTE]
> Można utworzyć pusty komunikat bez wartości, tylko "{}." Następnie tworzony jest szablon licencji z wartościami domyślnymi. Wartość domyślna działa w większości przypadków. W przypadku scenariuszy dostarczania licencji na podstawie firmy Microsoft należy zawsze używać wartości domyślnych. Jeśli musisz ustawić wartości "Provider" i "content_id", dostawca musi być zgodny z poświadczeniami Widevine.

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

## <a name="json-message"></a>Komunikat JSON

| Nazwa | Wartość | Opis |
| --- | --- | --- |
| payload |Ciąg zakodowany algorytmem Base64 |Żądanie licencji wysyłane przez klienta. |
| content_id |Ciąg zakodowany algorytmem Base64 |Identyfikator używany do wygenerowania identyfikatora klucza i klucza zawartości dla każdej content_key_specs. track_type. |
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
| content_key_specs. key_id |Binarne ciągi zakodowane algorytmem Base64, 16 bajtów |Unikatowy identyfikator klucza. |

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

Media Services udostępnia klasę, która umożliwia skonfigurowanie licencji Widevine. Aby utworzyć licencję, Przekaż kod JSON do [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Aby skonfigurować szablon, można:

### <a name="directly-construct-a-json-string"></a>Bezpośrednie konstruowanie ciągu JSON

Ta metoda może być podatna na błędy. Zaleca się użycie innej metody, opisanej w temacie [Definiowanie wymaganych klas i Serializacja do JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a>Zdefiniuj wymaganą klasę i serializować do formatu JSON

#### <a name="define-classes"></a>Definiuj klasy

W poniższym przykładzie przedstawiono przykład definicji klas, które są mapowane na schemat JSON Widevine. Można utworzyć wystąpienia klas przed ich serializacji do ciągu JSON.  

```csharp
public class PolicyOverrides
{
    public bool CanPlay { get; set; }
    public bool CanPersist { get; set; }
    public bool CanRenew { get; set; }
    public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
}

public class ContentKeySpec
{
    public string TrackType { get; set; }
    public int SecurityLevel { get; set; }
    public OutputProtection RequiredOutputProtection { get; set; }
}

public class OutputProtection
{
    public string HDCP { get; set; }
}

public class WidevineTemplate
{
    public string AllowedTrackTypes { get; set; }
    public ContentKeySpec[] ContentKeySpecs { get; set; }
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Skonfiguruj licencję

Użyj klas zdefiniowanych w poprzedniej sekcji, aby utworzyć kod JSON, który jest używany do konfigurowania [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

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

Zapoznaj się z tematem [Ochrona przy użyciu technologii DRM](protect-with-drm.md)
