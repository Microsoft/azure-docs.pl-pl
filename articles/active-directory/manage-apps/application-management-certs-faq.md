---
title: Azure Active Directory często zadawane pytania dotyczące certyfikatów zarządzania aplikacjami
description: Poznaj odpowiedzi na często zadawane pytania dotyczące zarządzania certyfikatami dla aplikacji przy użyciu Azure Active Directory jako dostawcy tożsamości (dostawcy tożsamości).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 928bf02e2d628379738483b40631e36f0f929176
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803808"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Certyfikaty zarządzania aplikacjami usługi Azure Active Directory (Azure AD) — często zadawane pytania

Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące zarządzania certyfikatami dla aplikacji za pomocą Azure Active Directory (Azure AD) jako dostawcy tożsamości (dostawcy tożsamości).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Czy istnieje sposób na wygenerowanie listy wygasających certyfikatów podpisywania SAML?

Możesz wyeksportować wszystkie rejestracje aplikacji z wygasaniem wpisów tajnych, certyfikatów i ich właścicieli dla określonych aplikacji z katalogu w pliku CSV za pomocą [skryptów programu PowerShell](app-management-powershell-samples.md). 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Gdzie można znaleźć informacje na temat wkrótce upływających informacji na temat odnawiania certyfikatów?

Procedurę można znaleźć [tutaj](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Jak mogę dostosować datę wygaśnięcia certyfikatów wystawionych przez usługę Azure AD?

Domyślnie usługa Azure AD konfiguruje certyfikat do wygaśnięcia po trzech latach, gdy jest tworzony automatycznie podczas konfigurowania logowania jednokrotnego za pomocą protokołu SAML. Ponieważ nie można zmienić daty certyfikatu po jego zapisaniu, należy utworzyć nowy certyfikat. Aby zapoznać się z krokami, jak to zrobić, zapoznaj się z tematem [Dostosowywanie daty wygaśnięcia certyfikatu federacyjnego i przekazanie go do nowego certyfikatu](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate).

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Jak można zautomatyzować powiadomienia o wygaśnięciu certyfikatów?

Usługa Azure AD wyśle wiadomość e-mail z powiadomieniem 60, 30 i 7 dni przed wygaśnięciem certyfikatu SAML. Możesz dodać więcej niż jeden adres e-mail, aby otrzymywać powiadomienia. 

> [!NOTE]
> Do listy powiadomień możesz dodać maksymalnie 5 adresów e-mail (w tym adres e-mail administratora, który dodał aplikację). Jeśli potrzebujesz więcej osób do powiadomienia, użyj wiadomości e-mail z listą dystrybucyjną. 

Aby określić wiadomości e-mail, na które mają być wysyłane powiadomienia, zobacz [Dodawanie adresów e-mail powiadomień dla wygaśnięcia certyfikatu](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration).

Nie ma możliwości edytowania ani dostosowywania powiadomień e-mail otrzymywanych z programu `aadnotification@microsoft.com` . Można jednak eksportować rejestracje aplikacji z wygasaniem wpisów tajnych i certyfikatów za pomocą [skryptów programu PowerShell](app-management-powershell-samples.md).

## <a name="who-can-update-the-certificates"></a>Kto może aktualizować certyfikaty?

Właściciel aplikacji lub administratora globalnego lub administratora aplikacji może aktualizować certyfikaty za pomocą interfejsu użytkownika Azure Portal, programu PowerShell lub Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Potrzebuję więcej szczegółowych informacji na temat opcji podpisywania certyfikatu.

W usłudze Azure AD można skonfigurować opcje podpisywania certyfikatu i algorytm podpisywania certyfikatu. Aby dowiedzieć się więcej, zobacz [Zaawansowane opcje podpisywania certyfikatu tokenu SAML dla aplikacji usługi Azure AD](certificate-signing-options.md).

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Muszę zastąpić certyfikat dla aplikacji serwer proxy aplikacji usługi Azure AD platformy Azure i potrzebujesz dodatkowych instrukcji.

Aby zastąpić certyfikaty dla aplikacji serwer proxy aplikacji usługi Azure AD platformy Azure, zobacz [przykład programu PowerShell — Zastąp certyfikat w aplikacjach serwera proxy aplikacji](scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Jak mogę zarządzać certyfikatami dla domen niestandardowych w usłudze Azure serwer proxy aplikacji usługi Azure AD?

Aby skonfigurować aplikację lokalną do korzystania z domeny niestandardowej, musisz mieć zweryfikowaną Azure Active Directory domenę niestandardową, certyfikat PFX dla domeny niestandardowej oraz aplikację lokalną do skonfigurowania. Aby dowiedzieć się więcej, zobacz [domeny niestandardowe na platformie Azure serwer proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Muszę zaktualizować certyfikat podpisywania tokenu po stronie aplikacji. Gdzie mogę uzyskać dostęp do usługi Azure AD?

Certyfikat SAML X. 509 można odnowić, zobacz [certyfikat podpisywania SAML](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Co to jest Przerzucanie klucza podpisywania usługi Azure AD?

Więcej informacji można znaleźć [tutaj](../develop/active-directory-signing-key-rollover.md). 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Jak mogę odnowić certyfikatu szyfrowania tokenu aplikacji?

Aby odnowić certyfikat szyfrowania tokenu aplikacji, zobacz [Jak odnowić certyfikat szyfrowania tokenu dla aplikacji przedsiębiorstwa](howto-saml-token-encryption.md). 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Jak mogę odnów certyfikat podpisywania tokenu aplikacji?

Aby odnowić certyfikat podpisywania tokenu aplikacji, zobacz [Jak odnowić token podpisywania certyfikatu dla aplikacji dla przedsiębiorstw](manage-certificates-for-federated-single-sign-on.md).

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Jak mogę zaktualizować usługę Azure AD po zmianie certyfikatów federacyjnych?

Aby zaktualizować usługę Azure AD po zmianie certyfikatów federacyjnych, zobacz [Odnawianie certyfikatów Federacji dla Microsoft 365 i Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md).
