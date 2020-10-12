---
title: Omówienie szablonu licencji PlayReady usługi Media Services
description: Ten temat zawiera omówienie szablonu licencji PlayReady, który jest używany do konfigurowania licencji PlayReady.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: fddce5d0-1278-478f-ae05-9b985c748731
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: df28078363162496672232ecdebe02fc99ce50f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89261057"
---
# <a name="media-services-playready-license-template-overview"></a>Omówienie szablonu licencji PlayReady usługi Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services teraz udostępnia usługę do dostarczania licencji PlayReady. Gdy gracz (na przykład Silverlight) próbuje odtworzyć zawartość chronioną przy użyciu oprogramowania PlayReady, do usługi dostarczania licencji jest wysyłane żądanie w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, wystawia licencję, która jest wysyłana do klienta i służy do odszyfrowania i odtworzenia określonej zawartości.

Media Services udostępnia również interfejsy API, których można użyć do konfigurowania licencji PlayReady. Licencje zawierają prawa i ograniczenia, które mają być wymuszane przez środowisko uruchomieniowe zarządzania prawami cyfrowymi (DRM) PlayReady, gdy użytkownik próbuje odtworzyć chronioną zawartość.
Poniżej przedstawiono kilka przykładów ograniczeń licencji PlayReady, które można określić:

* Data i godzina, z której licencja jest ważna.
* Wartość daty i godziny wygaśnięcia licencji. 
* W celu zapisania licencji w magazynie trwałym na komputerze klienckim. Licencje trwałe są zwykle używane, aby umożliwić odtwarzanie zawartości w trybie offline.
* Minimalny poziom zabezpieczeń wymagany przez odtwarzacz do odtwarzania zawartości. 
* Poziom ochrony danych wyjściowych dla kontrolek wynikowych dla zawartości audio\video. 
* Aby uzyskać więcej informacji, zobacz sekcję "formanty wyjściowe" (3,5) w dokumencie [reguły zgodności oprogramowania PlayReady](https://www.microsoft.com/playready/licensing/compliance/) .

> [!NOTE]
> Obecnie można skonfigurować tylko PlayRight licencji PlayReady. To prawo jest wymagane. PlayRight umożliwia klientowi odtwarzanie zawartości. Można również użyć PlayRight do skonfigurowania ograniczeń związanych z odtwarzaniem. Aby uzyskać więcej informacji, zobacz [PlayReadyPlayRight](media-services-playready-license-template-overview.md#PlayReadyPlayRight).
> 
> 

Aby skonfigurować licencje PlayReady przy użyciu Media Services, należy skonfigurować Media Services szablon licencji PlayReady. Szablon jest zdefiniowany w pliku XML.

W poniższym przykładzie przedstawiono najprostszy (i najbardziej typowy) szablon, który konfiguruje podstawową licencję przesyłania strumieniowego. Ta licencja umożliwia klientom odtwarzanie zawartości chronionej przez program PlayReady.

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

KOD XML jest zgodny ze schematem XML szablonu licencji PlayReady zdefiniowanym w sekcji "schemat XML szablonu licencji PlayReady".

Media Services definiuje również zestaw klas platformy .NET, których można użyć do serializacji i deserializacji do i z kodu XML. Opis głównych klas można znaleźć w [Media Services klas .NET](media-services-playready-license-template-overview.md#classes) , które są używane do konfigurowania szablonów licencji.

Aby uzyskać kompletny przykład, który używa klas platformy .NET do konfigurowania szablonu licencji PlayReady, zobacz [Korzystanie z szyfrowania dynamicznego PlayReady i usługi dostarczania licencji](media-services-protect-with-playready-widevine.md).

## <a name="media-services-net-classes-that-are-used-to-configure-license-templates"></a><a id="classes"></a>Media Services klas .NET, które są używane do konfigurowania szablonów licencji
Następujące klasy są głównymi klasami .NET, które są używane do konfigurowania szablonów licencji programu Media Services PlayReady. Te klasy są mapowane na typy zdefiniowane w [schemacie XML szablonu licencji PlayReady](media-services-playready-license-template-overview.md#schema).

Klasa [MediaServicesLicenseTemplateSerializer](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer?view=azure-dotnet#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_mediaserviceslicensetemplateserializer) jest używana do serializacji i deserializacji do i z pliku XML szablonu licencji Media Services.

### <a name="playreadylicenseresponsetemplate"></a>PlayReadyLicenseResponseTemplate
[PlayReadyLicenseResponseTemplate](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate?view=azure-dotnet#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadylicenseresponsetemplate): Ta klasa reprezentuje szablon odpowiedzi wysyłanej do użytkownika. Zawiera pole dla niestandardowego ciągu danych między serwerem licencji a aplikacją (co może być przydatne w przypadku niestandardowej logiki aplikacji). Zawiera również listę co najmniej jednego szablonu licencji.

Jako klasa najwyższego poziomu w hierarchii szablonów, szablon odpowiedzi zawiera listę szablonów licencji. Szablony licencji obejmują (bezpośrednio lub pośrednio) wszystkie inne klasy, które tworzą dane szablonu do serializacji.

### <a name="playreadylicensetemplate"></a>PlayReadyLicenseTemplate
[PlayReadyLicenseTemplate](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate?view=azure-dotnet#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadylicensetemplate): Ta klasa reprezentuje szablon licencji służący do tworzenia licencji PlayReady do zwrócenia użytkownikom. Zawiera dane z klucza zawartości w licencji. Zawiera również wszelkie prawa lub ograniczenia, które musi wymuszać środowisko uruchomieniowe oprogramowania PlayReady DRM, gdy jest używany klucz zawartości.

### <a name="playreadyplayright"></a><a id="PlayReadyPlayRight"></a>PlayReadyPlayRight
[PlayReadyPlayRight](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright?view=azure-dotnet#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadyplayright): Ta klasa reprezentuje PlayRight licencji PlayReady. Umożliwia użytkownikowi możliwość odtwarzania zawartości z uwzględnieniem ograniczeń skonfigurowanych w ramach licencji i samego PlayRight (dla zasad specyficznych dla odtwarzania). Większość zasad na PlayRight dotyczy ograniczeń wyjściowych, które kontrolują typy danych wyjściowych, do których można odfiltrować zawartość. Zawiera również wszelkie ograniczenia, które muszą być wprowadzane w przypadku użycia danych wyjściowych. Na przykład jeśli DigitalVideoOnlyContentRestriction jest włączona, środowisko uruchomieniowe DRM zezwala tylko na wyświetlanie wideo przy użyciu danych wyjściowych. (Analogowe wyjście wideo nie pozwala na przekazywanie zawartości).

> [!IMPORTANT]
> Te typy ograniczeń mogą być zaawansowane, ale mogą mieć również wpływ na środowisko użytkownika. Jeśli ochrona danych wyjściowych jest zbyt restrykcyjna, zawartość może być nieodtwarzana na niektórych klientach. Aby uzyskać więcej informacji, zobacz [reguły zgodności oprogramowania PlayReady](https://www.microsoft.com/playready/licensing/compliance/).
> 
> 

Aby zapoznać się z przykładem poziomów ochrony obsługiwanych przez program Silverlight, zobacz temat [Obsługa danych wyjściowych w programie Silverlight](https://go.microsoft.com/fwlink/?LinkId=617318).

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

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
