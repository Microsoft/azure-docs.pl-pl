---
title: Azure AD Connect funkcje i konfiguracja usługi synchronizacji | Microsoft Docs
description: Opisuje funkcje po stronie usługi dla usługi synchronizacji Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261ab5d0f039705a2566b7c28ff4c06778bb661a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410542"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funkcje usługi synchronizacji programu Azure AD Connect

Funkcja synchronizacji Azure AD Connect ma dwa składniki:

* Składnik lokalny o nazwie **Azure AD Connect Sync**, nazywany także **aparatem synchronizacji**.
* Usługa, która znajduje się w usłudze Azure AD, znana także jako **usługa synchronizacji Azure AD Connect**

W tym temacie wyjaśniono, jak działa następujące funkcje **usługi synchronizacji Azure AD Connect** oraz jak można je skonfigurować za pomocą programu Windows PowerShell.

Te ustawienia są konfigurowane przez [moduł Azure Active Directory dla środowiska Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100)). Pobierz i zainstaluj go niezależnie od Azure AD Connect. Polecenia cmdlet udokumentowane w tym temacie zostały wprowadzone w [wersji 2016 marca (kompilacja 9031,1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Jeśli nie masz poleceń cmdlet opisanych w tym temacie lub nie generują one tego samego wyniku, upewnij się, że uruchamiasz najnowszą wersję.

Aby wyświetlić konfigurację w katalogu usługi Azure AD, uruchom polecenie `Get-MsolDirSyncFeatures` .  
![Wynik Get-MsolDirSyncFeatures](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Wiele z tych ustawień można zmienić tylko przez Azure AD Connect.

Następujące ustawienia można skonfigurować przez `Set-MsolDirSyncFeature` :

| DirSyncFeature | Komentarz |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Umożliwia dołączenie obiektów do obiektu userPrincipalName poza podstawowym adresem SMTP. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Umożliwia aparatowi synchronizacji aktualizowanie atrybutu userPrincipalName dla użytkowników zarządzanych/licencjonowanych (niefederacyjnych). |

Po włączeniu funkcji nie można jej ponownie wyłączyć.

> [!NOTE]
> Od 24 sierpnia 2016 funkcja *odporności na zduplikowane atrybuty* jest domyślnie włączona dla nowych katalogów usługi Azure AD. Ta funkcja zostanie również udostępniona i włączona dla katalogów utworzonych przed tą datą. Otrzymasz wiadomość e-mail z powiadomieniem, gdy w katalogu zostanie włączona ta funkcja.
> 
> 

Następujące ustawienia są konfigurowane przez Azure AD Connect i nie mogą być modyfikowane przez `Set-MsolDirSyncFeature` :

| DirSyncFeature | Komentarz |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Włączanie zapisywania zwrotnego urządzeń](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect Synchronize: rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency <br/> DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Zezwala atrybutowi na poddawanie kwarantannie, gdy jest to duplikat innego obiektu, a nie niepowodzenie całego obiektu podczas eksportowania. |
| Synchronizacja skrótów haseł |[Implementowanie synchronizacji skrótów haseł z synchronizacją Azure AD Connect](how-to-connect-password-hash-synchronization.md) |
|Uwierzytelnianie przekazywane|[Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego usługi Azure Active Directory](how-to-connect-pta.md)|
| UnifiedGroupWriteback |Zapisywanie zwrotne grup|
| UserWriteback |Obecnie nie jest obsługiwane. |

## <a name="duplicate-attribute-resiliency"></a>Odporność na zduplikowane atrybuty

Zamiast nie można zainicjować obsługi obiektów ze zduplikowanymi Upnmi/proxyAddresses, zduplikowany atrybut jest "poddane kwarantannie" i przypisywana jest wartość tymczasowa. Po rozwiązaniu konfliktu tymczasowa nazwa UPN jest automatycznie zmieniana na właściwą. Aby uzyskać więcej informacji, zobacz [Synchronizacja tożsamości i odporność na zduplikowane atrybuty](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Dopasowanie niewygładzone UserPrincipalName

Gdy ta funkcja jest włączona, opcja Niestandardowa zgodność jest włączana dla nazwy UPN oprócz [podstawowego adresu SMTP](https://support.microsoft.com/kb/2641663), który jest zawsze włączony. Niezrównane dopasowanie służy do dopasowania istniejących użytkowników w chmurze w usłudze Azure AD z użytkownikami lokalnymi.

Jeśli zachodzi potrzeba dopasowania lokalnych kont usługi AD z istniejącymi kontami utworzonymi w chmurze i nie używasz usługi Exchange Online, ta funkcja jest przydatna. W tym scenariuszu zazwyczaj nie istnieje powód, aby ustawić atrybut SMTP w chmurze.

Ta funkcja jest domyślnie włączona w przypadku nowo utworzonych katalogów usługi Azure AD. Aby sprawdzić, czy ta funkcja jest włączona, możesz uruchomić:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Jeśli ta funkcja nie jest włączona dla katalogu usługi Azure AD, możesz ją włączyć, uruchamiając polecenie:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synchronizuj aktualizacje userPrincipalName

W przeszłości aktualizacje atrybutu UserPrincipalName przy użyciu usługi synchronizacji z lokalnego systemu zostały zablokowane, chyba że oba te warunki zostały spełnione:

* Użytkownik jest zarządzany (niefederacyjny).
* Użytkownikowi nie przypisano licencji.

> [!NOTE]
> Od marca 2019 można synchronizować zmiany nazw UPN dla kont użytkowników federacyjnych.
> 

Włączenie tej funkcji pozwala aparatowi synchronizacji aktualizować element userPrincipalName w przypadku jego zmiany w środowisku lokalnym i korzystać z funkcji synchronizacji skrótów haseł lub uwierzytelniania przekazywanego.

Ta funkcja jest domyślnie włączona w przypadku nowo utworzonych katalogów usługi Azure AD. Aby sprawdzić, czy ta funkcja jest włączona, możesz uruchomić:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Jeśli ta funkcja nie jest włączona dla katalogu usługi Azure AD, możesz ją włączyć, uruchamiając polecenie:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Po włączeniu tej funkcji istniejące wartości userPrincipalName pozostaną bez zmian. Przy następnej zmianie atrybutu userPrincipalName lokalnego, standardowa synchronizacja różnicowa dla użytkowników będzie aktualizować nazwę UPN.  

## <a name="see-also"></a>Zobacz też

* [Synchronizacja Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).