---
title: Azure AD Connect — Aktualizacja certyfikatu TLS/SSL dla farmy AD FS | Microsoft Docs
description: Ten dokument zawiera szczegółowe instrukcje dotyczące aktualizowania certyfikatu TLS/SSL farmy AD FS przy użyciu Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 451b50e70b98849dfc4654566d09a5a961abe451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279911"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aktualizowanie certyfikatu TLS/SSL dla farmy Active Directory Federation Services (AD FS)

## <a name="overview"></a>Omówienie
W tym artykule opisano, jak za pomocą Azure AD Connect zaktualizować certyfikat protokołu TLS/SSL dla farmy Active Directory Federation Services (AD FS). Za pomocą narzędzia Azure AD Connect można łatwo zaktualizować certyfikat protokołu TLS/SSL dla farmy AD FS, nawet jeśli wybrana metoda logowania użytkownika nie jest AD FS.

W trzech prostych krokach można wykonać całą operację aktualizowania certyfikatu TLS/SSL dla farmy AD FS na wszystkich serwerach federacyjnym i serwerze proxy aplikacji sieci Web (WAP):

![Trzy kroki](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Aby dowiedzieć się więcej o certyfikatach używanych przez AD FS, zobacz [Opis certyfikatów używanych przez AD FS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc730660(v=ws.11)).

## <a name="prerequisites"></a>Wymagania wstępne

* **Farma AD FS**: Upewnij się, że Farma AD FS jest w systemie Windows Server 2012 R2 lub nowszym.
* **Azure AD Connect**: Upewnij się, że wersja Azure AD Connect to 1.1.553.0 lub nowsza. Zostanie użyta aktualizacja zadania **AD FS certyfikat SSL**.

![Aktualizowanie zadania TLS](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Krok 1. Udostępnianie informacji o farmie AD FS

Azure AD Connect próbuje uzyskać informacje o farmie AD FS automatycznie przez:
1. Wykonywanie zapytania dotyczącego informacji farmy z AD FS (system Windows Server 2016 lub nowszy).
2. Odwołuje się do informacji z poprzednich przebiegów, które są przechowywane lokalnie w Azure AD Connect.

Możesz zmodyfikować listę serwerów, które są wyświetlane, dodając lub usuwając serwery, aby odzwierciedlić bieżącą konfigurację farmy AD FS. Gdy tylko zostaną podane informacje o serwerze, Azure AD Connect będzie wyświetlał połączenie i bieżący stan certyfikatu TLS/SSL.

![Informacje o serwerze AD FS](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Jeśli lista zawiera serwer, który nie jest już częścią farmy AD FS, kliknij przycisk **Usuń** , aby usunąć serwer z listy serwerów w farmie AD FS.

![Serwer w trybie offline na liście](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Usuwanie serwera z listy serwerów farmy AD FS w Azure AD Connect jest operacją lokalną i aktualizuje informacje dla farmy AD FS, która Azure AD Connect jest przechowywana lokalnie. Azure AD Connect nie modyfikuje konfiguracji AD FS w celu odzwierciedlenia zmiany.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Krok 2. dostarczenie nowego certyfikatu TLS/SSL

Po potwierdzeniu informacji o AD FS serwerów farmy Azure AD Connect monit o podanie nowego certyfikatu TLS/SSL. Podaj chroniony hasłem certyfikat PFX, aby kontynuować instalację.

![Certyfikat TLS/SSL](./media/how-to-connect-fed-ssl-update/certificate.png)

Po podaniu certyfikatu Azure AD Connect przechodzą przez serię wymagań wstępnych. Sprawdź certyfikat, aby upewnić się, że certyfikat jest prawidłowy dla farmy AD FS:

-   Nazwa podmiotu/Alternatywna nazwa podmiotu dla certyfikatu jest taka sama jak nazwa usługi federacyjnej lub jest to certyfikat z symbolem wieloznacznym.
-   Certyfikat jest ważny przez ponad 30 dni.
-   Łańcuch zaufania certyfikatów jest prawidłowy.
-   Certyfikat jest chroniony hasłem.

## <a name="step-3-select-servers-for-the-update"></a>Krok 3. Wybieranie serwerów do aktualizacji

W następnym kroku wybierz serwery, dla których należy zaktualizować certyfikat TLS/SSL. Serwery, które są w trybie offline, nie mogą zostać wybrane do aktualizacji.

![Wybierz serwery do zaktualizowania](./media/how-to-connect-fed-ssl-update/selectservers.png)

Po zakończeniu konfiguracji Azure AD Connect wyświetla komunikat informujący o stanie aktualizacji i udostępnia opcję weryfikacji AD FS logowania.

![Ukończono konfigurację](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Często zadawane pytania

* **Co powinna być nazwa podmiotu certyfikatu dla AD FS nowego certyfikatu protokołu TLS/SSL?**

    Azure AD Connect sprawdza, czy nazwa podmiotu/Alternatywna nazwa podmiotu certyfikatu zawiera nazwę usługi federacyjnej. Na przykład jeśli nazwa usługi federacyjnej to fs.contoso.com, nazwa podmiotu/Alternatywna nazwa podmiotu musi być fs.contoso.com.  Certyfikaty wieloznaczne są również akceptowane.

* **Dlaczego otrzymuję prośbę o podanie poświadczeń ponownie na stronie serwera WAP?**

    Jeśli poświadczenia zapewniane do łączenia się z serwerami AD FS nie mają również uprawnień do zarządzania serwerami WAP, Azure AD Connect monitowania o poświadczenia z uprawnieniami administracyjnymi na serwerach WAP.

* **Serwer jest wyświetlany jako offline. Co mam zrobić?**

    Azure AD Connect nie może wykonać żadnej operacji, jeśli serwer jest w trybie offline. Jeśli serwer jest częścią farmy AD FS, sprawdź łączność z serwerem. Po rozwiązaniu problemu naciśnij ikonę Odśwież, aby zaktualizować stan w kreatorze. Jeśli serwer był częścią farmy wcześniej, ale teraz już nie istnieje, kliknij przycisk **Usuń** , aby usunąć go z listy serwerów, które są obsługiwane przez Azure AD Connect. Usunięcie serwera z listy w Azure AD Connect nie powoduje zmiany konfiguracji AD FS. Jeśli używasz AD FS w systemie Windows Server 2016 lub nowszym, serwer pozostanie w ustawieniach konfiguracji i zostanie wyświetlony ponownie przy następnym uruchomieniu zadania.

* **Czy mogę zaktualizować podzestaw serwerów farmy z nowym certyfikatem TLS/SSL?**

    Tak. Można zawsze uruchomić ponownie **certyfikat SSL aktualizacji** zadania, aby zaktualizować pozostałe serwery. Na stronie **Wybieranie serwerów dla aktualizacji certyfikatu SSL** można sortować listę serwerów w dniu **wygaśnięcia protokołu SSL** , aby łatwo uzyskać dostęp do serwerów, które nie zostały jeszcze zaktualizowane.

* **Serwer został usunięty z poprzedniego przebiegu, ale nadal jest wyświetlany jako offline i wymieniony na stronie serwery AD FS. Dlaczego serwer w trybie offline nadal istnieje nawet po jego usunięciu?**

    Usunięcie serwera z listy w Azure AD Connect nie powoduje usunięcia go w konfiguracji AD FS. Odwołuje się do Azure AD Connect AD FS (system Windows Server 2016 lub nowszy) dla wszystkich informacji o farmie. Jeśli serwer nadal znajduje się w konfiguracji AD FS, zostanie on wyświetlony na liście.  

## <a name="next-steps"></a>Następne kroki

- [Azure AD Connect i Federacja](how-to-connect-fed-whatis.md)
- [Active Directory Federation Services zarządzanie i dostosowywanie za pomocą Azure AD Connect](how-to-connect-fed-management.md)