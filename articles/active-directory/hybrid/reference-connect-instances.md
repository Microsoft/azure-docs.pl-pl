---
title: 'Azure AD Connect: wystąpienia usługi synchronizacji | Microsoft Docs'
description: Ta strona dokumentuje specjalne zagadnienia dotyczące wystąpień usługi Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "66298832"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: specjalne zagadnienia dotyczące wystąpień
Azure AD Connect jest najczęściej używana w przypadku wystąpienia usługi Azure AD i pakietu Office 365 w całym świecie. Ale istnieją również inne wystąpienia i istnieją różne wymagania dotyczące adresów URL i innych specjalnych zagadnień.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Niemcy
[Microsoft Cloud Niemcy](https://www.microsoft.de/cloud-deutschland) to suwerenna chmura obsługiwana przez niemieckiego zarządcę danych.

| Adresy URL do otwarcia na serwerze proxy |
| --- |
| \*. microsoftonline.de |
| \*.windows.net |
| + Listy odwołania certyfikatów |

Gdy logujesz się do dzierżawy usługi Azure AD, musisz użyć konta w domenie onmicrosoft.de.

Funkcje obecnie nieobecne w Microsoft Cloud Niemcy:

* **Zapisywanie zwrotne haseł** jest dostępne w wersji zapoznawczej przy użyciu programu Azure AD Connect Version 1.1.570.0 i After.
* Inne usługi Azure AD — wersja Premium są niedostępne.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
[Chmura Microsoft Azure Government](https://azure.microsoft.com/features/gov/) jest chmurą dla instytucji rządowych USA.

Ta chmura była obsługiwana przez wcześniejsze wersje narzędzia DirSync. W przypadku kompilacji 1.1.180 z Azure AD Connect jest obsługiwana Kolejna generacja chmury. Ta generacja korzysta z punktów końcowych opartych na protokole US i ma inną listę adresów URL do otwarcia na serwerze proxy.

| Adresy URL do otwarcia na serwerze proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (wymagany do automatycznego wykrywania dzierżawy Azure Government) |
| \*. gov.us.microsoftonline.com |
| + Listy odwołania certyfikatów |

> [!NOTE]
> W przypadku Azure AD Connect w wersji 1.1.647.0 ustawienie wartości AzureInstance w rejestrze nie jest już wymagane pod warunkiem, że *. windows.net jest otwarty na serwerach proxy. Jednak w przypadku klientów, którzy nie zezwalają na połączenie z Internetem z serwerów Azure AD Connect, można użyć następującej konfiguracji ręcznej.

### <a name="manual-configuration"></a>Konfiguracja ręczna

Poniższe kroki konfiguracji ręcznej służą do zapewnienia, że Azure AD Connect używa Azure Government punktów końcowych synchronizacji.

1. Uruchom instalację Azure AD Connect.
2. Gdy zostanie wyświetlona pierwsza strona, na której zamierzasz zaakceptować umowę licencyjną, nie należy kontynuować pracy z uruchomionym kreatorem instalacji.
3. Uruchom regedit i Zmień klucz `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` rejestru na wartość. `4`
4. Wróć do Kreatora instalacji Azure AD Connect, zaakceptuj umowę licencyjną i Kontynuuj. Podczas instalacji upewnij się, że używasz ścieżki instalacji **niestandardowej konfiguracji** (a nie instalacji ekspresowej), a następnie kontynuuj instalację w zwykły sposób.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
