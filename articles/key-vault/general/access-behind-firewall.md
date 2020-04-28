---
title: Dostęp Key Vault za zaporą Azure Key Vault | Microsoft Docs
description: Dowiedz się, jak uzyskać dostęp do usługi Azure Key Vault z aplikacji znajdującej się za zaporą
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 5317111cf023316541f3435ff0d34450061209c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186377"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Uzyskiwanie dostępu do usługi Azure Key Vault za zaporą

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>Jakie porty, hosty lub adresy IP należy otworzyć, aby umożliwić aplikacji klienckiej magazynu kluczy za zaporę w celu uzyskania dostępu do magazynu kluczy?

Aby uzyskać dostęp do magazynu kluczy, aplikacja kliencka magazynu kluczy musi uzyskać dostęp do wielu punktów końcowych dla różnych funkcji:

* Uwierzytelnianie za pośrednictwem usługi Azure Active Directory (Azure AD).
* Zarządzanie usługą Azure Key Vault. Obejmuje to tworzenie, odczytywanie, aktualizowanie, usuwanie oraz ustawianie zasad dostępu za pośrednictwem usługi Azure Resource Manager.
* Uzyskiwanie dostępu do obiektów (kluczy i wpisów tajnych) przechowywanych w usłudze Key Vault i zarządzanie tymi obiektami, przechodząc przez punkt końcowy specyficzny dla usługi Key Vault (na przykład `https://yourvaultname.vault.azure.net`).  

W zależności od konfiguracji i środowiska istnieją pewne odstępstwa.

## <a name="ports"></a>Porty

Cały ruch do magazynu kluczy dla wszystkich trzech funkcji (uwierzytelnianie, zarządzanie i dostęp do warstwy danych) odbywa się przez protokół HTTPS: port 443. Jednak w przypadku listy CRL może czasami wystąpić ruch za pośrednictwem protokołu HTTP (port 80). Klienci, którzy obsługują protokół OCSP, nie powinni uzyskiwać dostępu do [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl)listy CRL, ale mogą czasami dotrzeć do programu.  

## <a name="authentication"></a>Authentication

W celu uwierzytelnienia aplikacja kliencka usługi Key Vault będzie musiała uzyskać dostęp do punktów końcowych usługi Azure Active Directory. Użyty punkt końcowy zależy od konfiguracji dzierżawy usługi Azure AD, typu nazwy głównej (nazwa główna użytkownika lub nazwa główna usługi) i typu konta (na przykład konto Microsoft lub konto służbowe).  

| Typ nazwy głównej | Punkt końcowy:port |
| --- | --- |
| Użytkownik korzystający z konta Microsoft<br> (na przykład user@hotmail.com) |**Globalne**<br> login.microsoftonline.com:443<br><br> **Chińska wersja platformy Azure:**<br> login.chinacloudapi.cn:443<br><br>**Wersja platformy Azure dla administracji USA:**<br> login.microsoftonline.us:443<br><br>**Azure (Niemcy):**<br> login.microsoftonline.de:443<br><br> i <br>login.live.com:443 |
| Nazwa główna użytkownika lub jednostka usługi przy użyciu konta służbowego z usługą Azure AD (na przykład user@contoso.com) |**Globalne**<br> login.microsoftonline.com:443<br><br> **Chińska wersja platformy Azure:**<br> login.chinacloudapi.cn:443<br><br>**Wersja platformy Azure dla administracji USA:**<br> login.microsoftonline.us:443<br><br>**Azure (Niemcy):**<br> login.microsoftonline.de:443 |
| Nazwa główna użytkownika lub jednostka usługi przy użyciu konta służbowego i punkt końcowy usług Active Directory Federation Services (AD FS) lub inny federacyjny punkt końcowy (na przykład user@contoso.com) |Wszystkie punkty końcowe dla konta służbowego oraz punkty końcowe usług AD FS lub inne federacyjne punkty końcowe |

Istnieją inne możliwe złożone scenariusze. Aby uzyskać dodatkowe informacje, zapoznaj się z artykułami [Azure Active Directory Authentication Flow](../../active-directory/develop/authentication-scenarios.md) (Przepływ uwierzytelniania w usłudze Azure Active Directory), [Integrating Applications with Azure Active Directory](../../active-directory/develop/active-directory-how-to-integrate.md) (Integrowanie aplikacji za pomocą usługi Azure Active Directory) i [Protokoły uwierzytelniania usługi Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx).  

## <a name="key-vault-management"></a>Zarządzanie usługą Key Vault

Aby zarządzać usługą Key Vault (akcje CRUD i ustawianie zasad dostępu), aplikacja kliencka magazynu kluczy musi uzyskać dostęp do punktu końcowego usługi Azure Resource Manager.  

| Typ operacji | Punkt końcowy:port |
| --- | --- |
| Operacje warstwy kontroli usługi Key Vault<br> za pośrednictwem usługi Azure Resource Manager |**Globalne**<br> management.azure.com:443<br><br> **Chińska wersja platformy Azure:**<br> management.chinacloudapi.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> management.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> management.microsoftazure.de:443 |
| Interfejs API programu Microsoft Graph |**Globalne**<br> graph.microsoft.com:443<br><br> **Chińska wersja platformy Azure:**<br> graph.chinacloudapi.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> graph.microsoft.com:443<br><br> **Azure (Niemcy):**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Operacje usługi Key Vault

We wszystkich przypadkach operacji kryptograficznych na obiektach (kluczach i wpisach tajnych) oraz zarządzania tymi obiektami klient magazynu kluczy musi uzyskać dostęp do punktu końcowego magazynu kluczy. Sufiks DNS punktu końcowego różni się w zależności od lokalizacji magazynu kluczy. Format punktu końcowego magazynu kluczy wygląda następująco: *nazwa magazynu*.*specyficzny dla regionu sufiks systemu DNS* zgodnie z opisem w poniższej tabeli.  

| Typ operacji | Punkt końcowy:port |
| --- | --- |
| Operacje, takie jak operacje kryptograficzne na kluczach, tworzenie, odczytywanie, aktualizowanie i usuwanie kluczy oraz wpisów tajnych, ustawianie lub pobieranie tagów i innych atrybutów obiektów magazynu kluczy (klucze lub wpisy tajne) |**Globalne**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Chińska wersja platformy Azure:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Azure (Niemcy):**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Zakresy adresów IP

Usługa Key Vault używa innych zasobów platformy Azure, takich jak infrastruktura PaaS. W związku z tym nie jest możliwe podanie konkretnego zakresu adresów IP, których będą używać punkty końcowe usługi Key Vault w określonym czasie. Jeśli zapora obsługuje tylko zakresy adresów IP, zapoznaj się z dokumentem [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Zakresy adresów IP centrów danych platformy Microsoft Azure). Authentication and Identity (w ramach usługi Azure Active Directory) to usługa globalna, która może przeprowadzić przełączenie w tryb failover do innych regionów lub przenieść ruch bez powiadomienia. W tym scenariuszu wszystkie zakresy IP znajdujące się na liście [Adresy IP usługi Authentication and Identity](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip) należy dodać do zapory.

## <a name="next-steps"></a>Następne kroki

Jeśli masz pytania dotyczące Key Vault, odwiedź [fora Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
