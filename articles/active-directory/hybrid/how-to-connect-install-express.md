---
title: 'Azure AD Connect: rozpoczynanie pracy przy użyciu ustawień ekspresowych | Microsoft Docs'
description: Dowiedz się, jak pobrać, zainstalować i uruchomić kreatora instalacji programu Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a655f355bb77d937f4daff2f8987769416ebd8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279673"
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Rozpoczynanie pracy z programem Azure AD Connect przy użyciu ustawień ekspresowych
Użyj **ustawień ekspresowych** programu Azure AD Connect, jeśli używasz topologii z jednym lasem oraz [synchronizacji skrótów haseł](how-to-connect-password-hash-synchronization.md) na potrzeby uwierzytelniania. **Ustawienia ekspresowe** to opcja domyślna używana w przypadku najbardziej typowych scenariuszy wdrożenia. Wystarczy kilka kliknięć, aby rozszerzyć katalog lokalny do chmury.

Przed rozpoczęciem instalacji należy [pobrać program Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) i wykonać czynności związane z wymaganiami wstępnymi opisane w temacie [Azure AD Connect: sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md).

Jeśli ustawienia ekspresowe nie są odpowiednie do Twojej topologii, zobacz [dokumentację pokrewną](#related-documentation), aby zapoznać się z innymi scenariuszami.

## <a name="express-installation-of-azure-ad-connect"></a>Ekspresowa instalacja programu Azure AD Connect
Możesz zobaczyć te czynności w praktyce, korzystając z sekcji [Filmy wideo](#videos).

1. Zaloguj się jako administrator lokalny na serwerze, na którym chcesz zainstalować program Azure AD Connect. Powinien być to serwer, który ma być serwerem synchronizacji.
2. Przejdź do pozycji **AzureADConnect.msi** i kliknij ją dwukrotnie.
3. Na ekranie powitalnym zaznacz pole wyrażenia zgody na warunki licencji i kliknij pozycję **Kontynuuj**.  
4. Na ekranie Ustawienia ekspresowe kliknij polecenie **Użyj ustawień ekspresowych**.  
   ![Wprowadzenie do programu Azure AD Connect](./media/how-to-connect-install-express/express.png)
5. Na ekranie Łączenie z usługą Azure AD wprowadź nazwę użytkownika i hasło administratora globalnego usługi Azure AD. Kliknij przycisk **Dalej**.  
   ![Łączenie z usługą Azure AD](./media/how-to-connect-install-express/connectaad.png)  
   Jeśli wystąpi błąd lub problemy z łącznością, zobacz [Rozwiązywanie problemów z łącznością](tshoot-connect-connectivity.md).
6. Na ekranie Łączenie z usługami AD DS wprowadź nazwę użytkownika i hasło konta administratora przedsiębiorstwa. Możesz wprowadzić domenę w formacie NetBios lub FQDN, czyli FABRIKAM\administrator lub fabrikam.com\administrator. Kliknij przycisk **Dalej**.  
   ![Łączenie z usługami AD DS](./media/how-to-connect-install-express/connectad.png)
7. Strona [**Konfiguracja logowania w usłudze Azure AD**](plan-connect-user-signin.md#azure-ad-sign-in-configuration) będzie wyświetlana tylko w przypadku, gdy nie ukończono [weryfikowania domen](../fundamentals/add-custom-domain.md) w ramach [wymagań wstępnych](how-to-connect-install-prerequisites.md).
   ![Niezweryfikowane domeny](./media/how-to-connect-install-express/unverifieddomain.png)  
   Jeśli zobaczysz tę stronę, sprawdź wszystkie domeny z oznaczeniem **Nie dodano** lub **Nie zweryfikowano**. Upewnij się, że używane domeny zostały zweryfikowane w usłudze Azure AD. Po zweryfikowaniu domen kliknij symbol Odśwież.
8. Na ekranie Wszystko gotowe do skonfigurowania kliknij pozycję **Zainstaluj**.
   * Opcjonalnie można na stronie Wszystko gotowe do skonfigurowania usunąć zaznaczenie pola wyboru **Uruchom proces synchronizacji, gdy tylko konfiguracja zostanie ukończona**. Usuń zaznaczenie tego pola wyboru, jeśli planujesz dodatkową konfigurację, na przykład konfigurację [filtrowania](how-to-connect-sync-configure-filtering.md). Jeśli ta opcja nie będzie zaznaczona, kreator skonfiguruje funkcję synchronizacji, ale nie włączy harmonogramu. Synchronizacja nie zostanie uruchomiona do momentu włączenia jej ręcznie przez [ponowne uruchomienie kreatora instalacji](how-to-connect-installation-wizard.md).
   * Pozostawienie włączonego pola wyboru **Uruchom proces synchronizacji, gdy tylko konfiguracja zostanie ukończona** spowoduje natychmiastowe wyzwolenie pełnej synchronizacji z usługą Azure AD wszystkich użytkowników, kontaktów i grup.
   * Jeśli korzystasz z programu Exchange z lokalną usługą Active Directory, możesz również włączyć [**Wdrożenie hybrydowe programu Exchange**](/exchange/exchange-hybrid). Włącz tę opcję, jeśli chcesz, aby skrzynki pocztowe programu Exchange były przechowywane jednocześnie zarówno w chmurze, jak i lokalnie.
     ![Wszystko gotowe do skonfigurowania programu Azure AD Connect](./media/how-to-connect-install-express/readytoconfigure.png)
9. Po zakończeniu instalacji kliknij przycisk **Zakończ**.
10. Po zakończeniu instalacji wyloguj się, a następnie zaloguj się ponownie przed użyciem narzędzia Synchronization Service Manager lub Synchronization Rule Editor.

## <a name="videos"></a>Wideo
Aby obejrzeć film wideo na temat instalacji ekspresowej, zobacz:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>Następne kroki
Po zainstalowaniu programu Azure AD Connect możesz [zweryfikować instalację i przypisać licencje](how-to-connect-post-installation.md).

Dowiedz się więcej na temat funkcji włączonych w ramach instalacji: [Automatyczne uaktualnianie](how-to-connect-install-automatic-upgrade.md), [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) oraz [Azure AD Connect Health](how-to-connect-health-sync.md).

Dowiedz się więcej na te popularne tematy: [harmonogram i sposób włączania synchronizacji](how-to-connect-sync-feature-scheduler.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

## <a name="related-documentation"></a>Dokumentacja pokrewna

| Temat | Łącze |
| --- | --- |
| Omówienie programu Azure AD Connect | [Integrowanie katalogów lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
| Instalowanie przy użyciu ustawień dostosowanych | [Niestandardowa instalacja programu Azure AD Connect](how-to-connect-install-custom.md) |
| Uaktualnianie przy użyciu narzędzia DirSync | [Uaktualnienie przy użyciu narzędzia Azure AD Sync (DirSync)](how-to-dirsync-upgrade-get-started.md)|
| Konta używane do instalacji | [Więcej informacji na temat poświadczeń i uprawnień dla programu Azure AD Connect](reference-connect-accounts-permissions.md) |