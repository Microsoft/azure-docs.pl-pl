---
title: Azure Active Directory często zadawane pytania dotyczące certyfikatów zarządzania aplikacją
description: Poznaj odpowiedzi na często zadawane pytania dotyczące zarządzania certyfikatami dla aplikacji przy użyciu usługi Azure Active Directory jako dostawcy tożsamości.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: iangithinji
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 0868c942a023662a1a6d3053477d85b0245fef4b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376243"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory (Azure AD) Application Management certificates frequently asked questions (Certyfikaty zarządzania aplikacją usługi Azure AD)

Na tej stronie znajdują się odpowiedzi na często zadawane pytania dotyczące zarządzania certyfikatami dla aplikacji przy użyciu usługi Azure Active Directory (Azure AD) jako dostawcy tożsamości.

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Czy istnieje sposób wygenerowania listy wygasających certyfikatów podpisywania SAML?

Możesz wyeksportować wszystkie rejestracje aplikacji z wygasających wpisów tajnych, certyfikatów i ich właścicieli dla określonych aplikacji z katalogu w pliku CSV za pomocą [skryptów programu PowerShell.](app-management-powershell-samples.md) 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Gdzie mogę znaleźć informacje o krokach odnawiania certyfikatów, które wkrótce wygasną?

Procedurę można znaleźć [tutaj](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Jak dostosować datę wygaśnięcia certyfikatów wystawionych przez usługę Azure AD?

Domyślnie usługa Azure AD konfiguruje certyfikat tak, aby wygasał po trzech latach od jego automatycznego utworzenia podczas konfigurowania logowania pojedynczego SAML. Ponieważ nie można zmienić daty certyfikatu po jego zapisaniu, należy utworzyć nowy certyfikat. Aby uzyskać instrukcje, jak to zrobić, zapoznaj się z tematem Customize [the expiration date for your federation certificate and roll it over to a new certificate](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)(Dostosowywanie daty wygaśnięcia certyfikatu federacji i przewłaczanie go do nowego certyfikatu).

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Jak zautomatyzować powiadomienia o wygaśnięciu certyfikatów?

Usługa Azure AD wyśle powiadomienie e-mail na 60, 30 i 7 dni przed wygaśnięciem certyfikatu SAML. Możesz dodać więcej niż jeden adres e-mail, aby otrzymywać powiadomienia. 

> [!NOTE]
> Do listy powiadomień można dodać maksymalnie 5 adresów e-mail (w tym adres e-mail administratora, który dodał aplikację). Jeśli chcesz, aby więcej osób było powiadamianych, użyj wiadomości e-mail z listą dystrybucyjną. 

Aby określić wiadomości e-mail, na które mają być wysyłane powiadomienia, zobacz Dodawanie adresów [powiadomień e-mail](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)w celu wygaśnięcia certyfikatu.

Nie ma możliwości edytowania ani dostosowywania tych powiadomień e-mail otrzymywanych z usługi `aadnotification@microsoft.com` . Można jednak wyeksportować rejestracje aplikacji z wygasających wpisów tajnych i certyfikatów za pomocą [skryptów programu PowerShell.](app-management-powershell-samples.md)

## <a name="who-can-update-the-certificates"></a>Kto może aktualizować certyfikaty?

Właściciel aplikacji, administrator globalny lub administrator aplikacji może zaktualizować certyfikaty za pomocą interfejsu Azure Portal użytkownika, programu PowerShell lub Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Potrzebuję więcej szczegółów na temat opcji podpisywania certyfikatów.

W usłudze Azure AD można skonfigurować opcje podpisywania certyfikatów i algorytm podpisywania certyfikatów. Aby dowiedzieć się więcej, zobacz [Zaawansowane opcje podpisywania certyfikatu tokenu SAML dla aplikacji usługi Azure AD.](certificate-signing-options.md)

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Chcę zastąpić certyfikat dla aplikacji usługi Azure AD serwer proxy aplikacji i potrzebuję więcej instrukcji.

Aby zastąpić certyfikaty dla aplikacji usługi Azure AD serwer proxy aplikacji, zobacz PowerShell sample - Replace certificate in serwer proxy aplikacji apps (Przykład [programu PowerShell — zastępowanie](scripts/powershell-get-custom-domain-replace-cert.md)certyfikatu w serwer proxy aplikacji aplikacji).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Jak mogę zarządzać certyfikatami dla domen niestandardowych w usłudze Azure AD serwer proxy aplikacji?

Aby skonfigurować aplikację lokalną do korzystania z domeny niestandardowej, musisz skonfigurować zweryfikowaną domenę niestandardową Azure Active Directory, certyfikat PFX dla domeny niestandardowej i aplikację lokalną. Aby dowiedzieć się więcej, zobacz [Domeny niestandardowe w usłudze Azure AD serwer proxy aplikacji](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Chcę zaktualizować certyfikat podpisywania tokenu po stronie aplikacji. Gdzie mogę uzyskać ją po stronie usługi Azure AD?

Certyfikat SAML X.509 można odnowić. Zobacz [Certyfikat podpisywania SAML.](configure-saml-single-sign-on.md#saml-signing-certificate)

## <a name="what-is-azure-ad-signing-key-rollover"></a>Co to jest przewłaczanie klucza podpisywania usługi Azure AD?

Więcej szczegółów można znaleźć [tutaj.](../develop/active-directory-signing-key-rollover.md) 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Jak mogę odnowić certyfikat szyfrowania tokenu aplikacji?

Aby odnowić certyfikat szyfrowania tokenu aplikacji, zobacz Jak odnowić certyfikat szyfrowania [tokenu dla aplikacji przedsiębiorstwa.](howto-saml-token-encryption.md) 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Jak mogę odnowić certyfikat podpisywania tokenu aplikacji?

Aby odnowić certyfikat podpisywania tokenu aplikacji, zobacz [Jak odnowić certyfikat podpisywania tokenu dla aplikacji przedsiębiorstwa.](manage-certificates-for-federated-single-sign-on.md)

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Jak mogę zaktualizować usługę Azure AD po zmianie certyfikatów federacji?

Aby zaktualizować usługę Azure AD po zmianie certyfikatów federacji, zobacz [Renew federation certificates for Microsoft 365 and Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md).
