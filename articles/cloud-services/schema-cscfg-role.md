---
title: Schemat roli usługi Azure Cloud Services (klasyczny) | Microsoft Docs
description: Element role pliku konfiguracji usługi określa liczbę wystąpień roli do wdrożenia dla każdej roli, wartości konfiguracji i odcisków palców certyfikatów.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd46a37ada1fb96797faee6c8491359561e065d7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934159"
---
# <a name="azure-cloud-services-classic-config-role-schema"></a>Schemat roli konfiguracji usługi Azure Cloud Services (klasyczny)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

`Role`Element pliku konfiguracji określa liczbę wystąpień roli do wdrożenia dla każdej roli w usłudze, wartości ustawień konfiguracji i odcisków palców dla wszystkich certyfikatów skojarzonych z rolą.

Aby uzyskać więcej informacji na temat schematu konfiguracji usługi platformy Azure, zobacz [Schemat konfiguracji usługi w chmurze (klasycznej)](schema-cscfg-file.md). Aby uzyskać więcej informacji na temat schematu definicji usługi platformy Azure, zobacz [schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> Element roli
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

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji usługi w chmurze (klasycznej)](schema-cscfg-file.md)