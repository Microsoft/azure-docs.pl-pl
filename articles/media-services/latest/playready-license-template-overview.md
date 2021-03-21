---
title: Media Services szablon licencji Microsoft PlayReady
description: Dowiedz się więcej o Azure Media Services wersji 3 z szablonem licencji PlayReady oraz o sposobie konfigurowania licencji PlayReady.
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 23b0f92a4beca6e1c986993a42cc8ac66e619177
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455437"
---
# <a name="media-services-v3-with-playready-license-template"></a>Media Services v3 z szablonem licencji PlayReady

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services umożliwia szyfrowanie zawartości za pomocą **oprogramowania Microsoft PlayReady**. Media Services udostępnia również usługę do dostarczania licencji PlayReady. Za pomocą interfejsów API Media Services można konfigurować licencje PlayReady. Gdy gracz podejmie próbę odtworzenia zawartości chronionej przez program PlayReady, żądanie jest wysyłane do usługi dostarczania licencji w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, wystawia licencję, która jest wysyłana do klienta i służy do odszyfrowania i odtworzenia określonej zawartości.

Licencje PlayReady zawierają prawa i ograniczenia, które mają zostać wymuszone przez środowisko uruchomieniowe zarządzania prawami cyfrowymi (DRM) PlayReady, gdy użytkownik próbuje odtworzyć chronioną zawartość. Poniżej przedstawiono kilka przykładów ograniczeń licencji PlayReady, które można określić:

* Data i godzina, z której licencja jest ważna.
* Wartość daty i godziny wygaśnięcia licencji. 
* W celu zapisania licencji w magazynie trwałym na komputerze klienckim. Licencje trwałe są zwykle używane, aby umożliwić odtwarzanie zawartości w trybie offline.
* Minimalny poziom zabezpieczeń wymagany przez odtwarzacz do odtwarzania zawartości. 
* Poziom ochrony danych wyjściowych dla kontrolek wynikowych dla zawartości audio\video. 
* Aby uzyskać więcej informacji, zobacz sekcję "formanty wyjściowe" (3,5) w dokumencie [reguły zgodności oprogramowania PlayReady](https://www.microsoft.com/playready/licensing/compliance/) .

> [!NOTE]
> Obecnie można skonfigurować tylko PlayRight licencji PlayReady. To prawo jest wymagane. PlayRight umożliwia klientowi odtwarzanie zawartości. Można również użyć PlayRight do skonfigurowania ograniczeń związanych z odtwarzaniem. 
> 

W tym temacie opisano sposób konfigurowania licencji PlayReady przy użyciu Media Services.

## <a name="basic-streaming-license-example"></a>Przykład podstawowej licencji przesyłania strumieniowego

W poniższym przykładzie przedstawiono najprostszy (i najbardziej typowy) szablon, który konfiguruje podstawową licencję przesyłania strumieniowego. Ta licencja umożliwia klientom odtwarzanie zawartości chronionej przez program PlayReady. 

KOD XML jest zgodny ze schematem XML szablonu licencji PlayReady zdefiniowanym w sekcji [schematu XML szablonu licencji PlayReady](#schema) .

```xml
<?xml version="1.0" encoding="utf-8"?>
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" 
                                  xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
    <LicenseTemplates>
    <PlayReadyLicenseTemplate>
        <ContentKey i:type="ContentEncryptionKeyFromHeader" />
        <PlayRight />
    </PlayReadyLicenseTemplate>
    </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

## <a name="use-media-services-apis-to-configure-license-templates"></a><a id="classes"></a>Konfigurowanie szablonów licencji przy użyciu interfejsów API Media Services

Media Services zawiera typy, których można użyć do skonfigurowania szablonu licencji PlayReady. 

Poniższy fragment kodu używa Media Services klas .NET do konfigurowania szablonu licencji PlayReady. Klasy są zdefiniowane w przestrzeni nazw [Microsoft. Azure. Management. Media. models](/dotnet/api/microsoft.azure.management.media.models) . Fragment kodu konfiguruje PlayRight licencji PlayReady. PlayRight umożliwia użytkownikowi odtworzenie zawartości z uwzględnieniem ograniczeń skonfigurowanych w licencji i na samym PlayRight (w przypadku zasad specyficznych dla odtwarzania). Większość zasad na PlayRight dotyczy ograniczeń wyjściowych, które kontrolują typy danych wyjściowych, do których można odfiltrować zawartość. Zawiera również wszelkie ograniczenia, które muszą być wprowadzane w przypadku użycia danych wyjściowych. Na przykład jeśli DigitalVideoOnlyContentRestriction jest włączona, środowisko uruchomieniowe DRM zezwala tylko na wyświetlanie wideo przy użyciu danych wyjściowych. (Analogowe wyjście wideo nie pozwala na przekazywanie zawartości).

> [!IMPORTANT]
> Licencja PlayReady ma ograniczenia, które są zaawansowane. Jeśli ochrona danych wyjściowych jest zbyt restrykcyjna, zawartość może być nieodtwarzana na niektórych klientach. Aby uzyskać więcej informacji, zobacz [reguły zgodności oprogramowania PlayReady](https://www.microsoft.com/playready/licensing/compliance/).

### <a name="configure-playready-license-template-with-net"></a>Konfigurowanie szablonu licencji PlayReady przy użyciu platformy .NET

```csharp
ContentKeyPolicyPlayReadyLicense objContentKeyPolicyPlayReadyLicense;
objContentKeyPolicyPlayReadyLicense = new ContentKeyPolicyPlayReadyLicense
{
    AllowTestDevices = true,
    BeginDate = new DateTime(2016, 1, 1),
    ContentKeyLocation = new ContentKeyPolicyPlayReadyContentEncryptionKeyFromHeader(),
    ContentType = ContentKeyPolicyPlayReadyContentType.UltraVioletStreaming,
    LicenseType = drmSettings.EnableOfflineMode ? ContentKeyPolicyPlayReadyLicenseType.Persistent : ContentKeyPolicyPlayReadyLicenseType.NonPersistent,
    PlayRight = new ContentKeyPolicyPlayReadyPlayRight
    {
        ImageConstraintForAnalogComponentVideoRestriction = true,
        ExplicitAnalogTelevisionOutputRestriction = new ContentKeyPolicyPlayReadyExplicitAnalogTelevisionRestriction(true, 2),
        AllowPassingVideoContentToUnknownOutput = ContentKeyPolicyPlayReadyUnknownOutputPassingOption.Allowed,
        FirstPlayExpiration = TimeSpan.FromSeconds(20.0),
    }
};
```

## <a name="playready-license-template-xml-schema"></a><a id="schema"></a>Schemat XML szablonu licencji PlayReady

```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
    <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
    <xs:complexType name="AgcAndColorStripeRestriction">
        <xs:sequence>
            <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
    <xs:simpleType name="ContentType">
        <xs:restriction base="xs:string">
            <xs:enumeration value="Unspecified" />
            <xs:enumeration value="UltravioletDownload" />
            <xs:enumeration value="UltravioletStreaming" />
        </xs:restriction>
    </xs:simpleType>
    <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
    <xs:complexType name="ExplicitAnalogTelevisionRestriction">
        <xs:sequence>
            <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
            <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
    <xs:complexType name="PlayReadyContentKey">
        <xs:sequence />
    </xs:complexType>
    <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
    <xs:complexType name="ContentEncryptionKeyFromHeader">
        <xs:complexContent mixed="false">
            <xs:extension base="tns:PlayReadyContentKey">
                <xs:sequence />
            </xs:extension>
        </xs:complexContent>
    </xs:complexType>
    <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
    <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
        <xs:complexContent mixed="false">
            <xs:extension base="tns:PlayReadyContentKey">
                <xs:sequence>
                    <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
                </xs:sequence>
            </xs:extension>
        </xs:complexContent>
    </xs:complexType>
    <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
    <xs:complexType name="PlayReadyLicenseResponseTemplate">
        <xs:sequence>
            <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
            <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
        </xs:sequence>
    </xs:complexType>
    <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
    <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
        <xs:sequence>
            <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
    <xs:complexType name="PlayReadyLicenseTemplate">
        <xs:sequence>
            <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
            <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
            <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
            <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
    <xs:simpleType name="PlayReadyLicenseType">
        <xs:restriction base="xs:string">
            <xs:enumeration value="Nonpersistent" />
            <xs:enumeration value="Persistent" />
        </xs:restriction>
    </xs:simpleType>
    <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
    <xs:complexType name="PlayReadyPlayRight">
        <xs:sequence>
            <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
        </xs:sequence>
    </xs:complexType>
    <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
    <xs:simpleType name="UnknownOutputPassingOption">
        <xs:restriction base="xs:string">
            <xs:enumeration value="NotAllowed" />
            <xs:enumeration value="Allowed" />
            <xs:enumeration value="AllowedWithVideoConstriction" />
        </xs:restriction>
    </xs:simpleType>
    <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
    <xs:complexType name="ScmsRestriction">
        <xs:sequence>
            <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
</xs:schema>
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tematem [Ochrona przy użyciu technologii DRM](protect-with-drm.md)
