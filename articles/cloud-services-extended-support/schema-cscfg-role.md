---
title: Schemat roli usługi Azure Cloud Services (obsługa rozszerzona) | Microsoft Docs
description: Informacje dotyczące schematu roli dla Cloud Services (obsługa rozszerzona)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744449"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Schemat roli konfiguracji usługi Azure Cloud Services (obsługa rozszerzona)

`Role`Element pliku konfiguracji określa liczbę wystąpień roli do wdrożenia dla każdej roli w usłudze, wartości ustawień konfiguracji i odcisków palców dla wszystkich certyfikatów skojarzonych z rolą.

Aby uzyskać więcej informacji na temat schematu konfiguracji usługi platformy Azure, zobacz [Schemat konfiguracji usługi w chmurze (obsługa rozszerzona)](schema-cscfg-file.md). Aby uzyskać więcej informacji na temat schematu definicji usługi platformy Azure, zobacz [schemat definicji usługi w chmurze (obsługa rozszerzona)](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> element roli
Poniższy przykład pokazuje `Role` element i jego elementy podrzędne.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

W poniższej tabeli opisano atrybuty `Role` elementu.

| Atrybut | Opis |
| --------- | ----------- |
| name   | Wymagane. Określa nazwę roli. Nazwa musi być zgodna z nazwą podaną dla roli w pliku definicji usługi.|
| vmName | Opcjonalny. Określa nazwę DNS maszyny wirtualnej. Nazwa może składać się z maksymalnie 10 znaków.|

W poniższej tabeli opisano elementy podrzędne `Role` elementu.

| Element | Opis |
| ------- | ----------- |
| Wystąpienia | Wymagane. Określa liczbę wystąpień do wdrożenia dla roli. Liczba wystąpień jest definiowana przez liczbę całkowitą dla `count` atrybutu.|
| Ustawienie   | Opcjonalny. Określa nazwę i wartość ustawienia w kolekcji ustawień dla roli. Nazwa ustawienia jest definiowana przez ciąg dla `name` atrybutu, a wartość ustawienia jest definiowana przez ciąg dla `value` atrybutu.|
| Certyfikat | Opcjonalny. Określa nazwę, odcisk palca i algorytm certyfikatu usługi, który ma zostać skojarzony z rolą. Nazwa certyfikatu jest definiowana przez ciąg dla `name` atrybutu. Odcisk palca certyfikatu jest definiowany przez ciąg liczb szesnastkowych zawierających spacje dla `thumbprint` atrybutu. Liczby szesnastkowe muszą być reprezentowane przy użyciu cyfr i wielkich liter w znakach alfanumerycznych. Algorytm certyfikatu jest definiowany przez ciąg dla `thumbprintAlgorithm` atrybutu.|

## <a name="see-also"></a>Zobacz także
[Schemat konfiguracji usługi w chmurze (obsługa rozszerzona)](schema-cscfg-file.md).