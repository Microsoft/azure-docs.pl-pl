---
title: 'Azure AD Connect: zapisywanie zwrotne grup'
description: W tym artykule opisano zapisywanie zwrotne grup w Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2e24246c749978cd2bbb5b3d0821eea6d7dfb4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660873"
---
# <a name="azure-ad-connect-group-writeback"></a>Zapisywanie zwrotne grup Azure AD Connect

Stornowanie grup umożliwia klientom korzystanie z grup chmur dla potrzeb hybrydowych. Jeśli używasz funkcji grup Microsoft 365, możesz mieć te grupy reprezentowane w Active Directory lokalnym. Ta opcja jest dostępna **tylko** wtedy, gdy program Exchange jest obecny w Active Directory lokalnym.

## <a name="pre-requisites"></a>Wymagania wstępne
Aby włączyć funkcję zapisywania zwrotnego grup, należy spełnić następujące wymagania wstępne.
- Azure Active Directory — wersja Premium licencji dla dzierżawy.
- Skonfigurowane wdrożenie hybrydowe między organizacją lokalną programu Exchange i Microsoft 365 i zweryfikowane, działa poprawnie.
- Zainstalowano obsługiwaną wersję lokalnego programu Exchange
- Skonfigurowano Logowanie jednokrotne przy użyciu Azure Active Directory Connect 

## <a name="enable-group-writeback"></a>Włączanie zapisywania zwrotnego grup
Aby włączyć funkcję zapisywania zwrotnego grup, wykonaj następujące czynności:

1. Otwórz Kreatora Azure AD Connect, wybierz pozycję **Konfiguruj** , a następnie kliknij przycisk **dalej**.
2. Wybierz opcję **Dostosuj opcje synchronizacji** , a następnie kliknij przycisk **dalej**.
3. Na stronie **Połącz z usługą Azure AD** wprowadź swoje poświadczenia. Kliknij przycisk **Dalej**.
4. Na stronie **funkcje opcjonalne** upewnij się, że opcje, które zostały wcześniej skonfigurowane, są nadal zaznaczone.
5. Wybierz pozycję **zapisywanie zwrotne grup** , a następnie kliknij przycisk **dalej**.
6. Na **stronie zapisywanie zwrotne**wybierz Active Directory jednostkę organizacyjną (OU) do przechowywania obiektów synchronizowanych z Microsoft 365 do organizacji lokalnej, a następnie kliknij przycisk **dalej**.
7. Na stronie **gotowy** do skonfigurowania kliknij pozycję **Konfiguruj**.
8. Po zakończeniu pracy kreatora kliknij przycisk **Zakończ** na stronie Konfiguracja ukończona.
9. Otwórz program Windows PowerShell jako administrator na serwerze Azure Active Directory Connect i uruchom następujące polecenia.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN
```

Aby uzyskać dodatkowe informacje na temat konfigurowania grup Microsoft 365, zobacz [Konfigurowanie grup Microsoft 365 przy użyciu hybrydowego programu Exchange](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect).

## <a name="disabling-group-writeback"></a>Wyłączanie zapisywania zwrotnego grup
Aby wyłączyć funkcję zapisywania zwrotnego grup, wykonaj następujące czynności: 


1. Uruchom Kreatora Azure Active Directory Connect i przejdź do strony dodatkowe zadania. Wybierz zadanie **Dostosuj opcje synchronizacji** , a następnie kliknij przycisk **dalej**.
2. Na stronie **funkcje opcjonalne** Usuń zaznaczenie pola wyboru Grupuj jako zwrotne.  Zostanie wyświetlone ostrzeżenie informujące o tym, że grupy zostaną usunięte.  Kliknij przycisk **Yes** (Tak).
   >[!IMPORTANT]
   > Wyłączenie zapisywania zwrotnego grup spowoduje, że wszystkie grupy, które zostały wcześniej utworzone przez tę funkcję, zostaną usunięte z lokalnej Active Directory w następnym cyklu synchronizacji. 

   ![Usuń zaznaczenie pola](media/how-to-connect-group-writeback/group2.png)
  
3. Kliknij przycisk **Dalej**.
4. Kliknij przycisk **Konfiguruj**.

 >[!NOTE]
 > Wyłączenie zapisywania zwrotnego grup ustawi wszystkie flagi importu i pełnej synchronizacji na wartość "true" na łączniku Azure Active Directory, powodując zmianę reguły na propagację w następnym cyklu synchronizacji, usuwając grupy, które zostały wcześniej zapisaną z powrotem do Active Directory.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).