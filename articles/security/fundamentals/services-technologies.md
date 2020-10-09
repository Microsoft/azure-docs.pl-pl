---
title: Usługi i technologie zabezpieczeń platformy Azure | Microsoft Docs
description: Artykuł zawiera zanadzorowana listę usług i technologii zabezpieczeń platformy Azure.
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: terrylan
ms.openlocfilehash: 83548063a796401792bd19e7ec27be36a0efa4e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80549208"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Usługi i technologie zabezpieczeń dostępne na platformie Azure

W naszych dyskusjach z bieżącymi i przyszłymi klientami platformy Azure często pytamy "czy masz listę wszystkich usług związanych z zabezpieczeniami i technologii oferowanych przez platformę Azure?".

Podczas oceniania opcji dostawcy usług w chmurze warto uzyskać te informacje. Ta lista została udostępniona, aby rozpocząć pracę.

Wraz z upływem czasu ta lista ulegnie zmianie i zwiększeniu, podobnie jak w przypadku platformy Azure. Pamiętaj, aby regularnie sprawdzać Tę stronę, aby zachować aktualność w naszych usługach i technologiach związanych z bezpieczeństwem.

## <a name="general-azure-security"></a>Ogólne zabezpieczenia platformy Azure
|Usługa|Opis|
|--------|--------|
|[&nbsp;Centrum zabezpieczeń &nbsp; Azure](/azure/security-center/security-center-intro)| Rozwiązanie do ochrony obciążeń w chmurze, które zapewnia zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami w ramach obciążeń chmury hybrydowej.|
|[Usługa Azure Key Vault](/azure/key-vault/key-vault-overview)| Bezpieczne przechowywanie wpisów tajnych dla haseł, parametrów połączenia i innych informacji potrzebnych do działania aplikacji. |
|[Dzienniki Azure Monitor](/azure/log-analytics/log-analytics-overview)|Usługa monitorowania, która zbiera dane telemetryczne i inne, oraz udostępnia język zapytań i aparat analityczny do dostarczania usługi Operational Insights dla aplikacji i zasobów. Może być używany samodzielnie lub z innymi usługami, takimi jak Security Center. |
|[Azure Dev/Test Labs](/azure/lab-services/devtest-lab-overview)|Usługa, która pomaga deweloperom i testerom szybko tworzyć środowiska na platformie Azure, jednocześnie minimalizując straty i kontrolując koszty.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Zabezpieczenia magazynu
|Usługa|Opis|
|------|--------|
| [&nbsp; &nbsp; Szyfrowanie usługi Azure &nbsp; Storage](/azure/storage/common/storage-service-encryption)|Funkcja zabezpieczeń, która automatycznie szyfruje dane w usłudze Azure Storage.   |
|[StorSimple zaszyfrowanego magazynu hybrydowego](/azure/storsimple/storsimple-ova-overview)| Zintegrowane rozwiązanie magazynu, które zarządza zadaniami magazynu między urządzeniami lokalnymi i magazynem w chmurze platformy Azure.|
|[Szyfrowanie Client-Side platformy Azure](/azure/storage/common/storage-client-side-encryption)| Rozwiązanie do szyfrowania po stronie klienta, które szyfruje dane w aplikacjach klienckich przed przekazaniem ich do usługi Azure Storage; Ponadto odszyfrowuje dane podczas pobierania. |
| [Sygnatury dostępu współdzielonego usługi Azure Storage](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Sygnatura dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu.  |
|[Klucze konta usługi Azure Storage](/azure/storage/common/storage-create-storage-account)| Metoda kontroli dostępu do usługi Azure Storage, która jest używana do uwierzytelniania podczas uzyskiwania dostępu do konta magazynu. |
|[Udziały plików platformy Azure z szyfrowaniem SMB 3,0](/azure/storage/files/storage-files-introduction)|Technologia zabezpieczeń sieci, która umożliwia automatyczne szyfrowanie sieci na potrzeby protokołu udostępniania plików bloku komunikatów serwera (SMB). |
|[analityka magazynu platformy Azure](/rest/api/storageservices/Storage-Analytics)| Rejestrowanie i technologia generowania metryk dla danych na koncie magazynu. |

<!------>

## <a name="database-security"></a>Zabezpieczenia bazy danych
|Usługa|Opis|
|------|--------|
| [&nbsp;Zapora Azure SQL &nbsp;](/azure/sql-database/sql-database-firewall-configure)|Funkcja kontroli dostępu do sieci chroniąca przed atakami opartymi na sieci do bazy danych programu. |
|[&nbsp;Szyfrowanie na &nbsp; poziomie komórek SQL &nbsp; Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Technologia zabezpieczeń bazy danych, która zapewnia szyfrowanie na poziomie szczegółowości.  |
| [&nbsp; &nbsp; Szyfrowanie połączenia usługi Azure SQL](/azure/sql-database/sql-database-control-access)|Aby zapewnić bezpieczeństwo, usługa SQL Database kontroluje dostęp za pomocą reguł zapory, ograniczając adresy IP dla połączeń, stosując mechanizmy uwierzytelniania wymagające potwierdzenia tożsamości przez użytkowników oraz mechanizmy autoryzacji ograniczające użytkowników do określonych działań i danych. |
| [Zawsze szyfrowanie w usłudze Azure SQL](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Chroni dane poufne, takie jak numery kart kredytowych lub krajowe numery identyfikacyjne (na przykład numery ubezpieczenia społecznego w Stanach Zjednoczonych), przechowywane w bazie danych Azure SQL Database lub SQL Server.  |
| [&nbsp;Transparent Data Encryption Azure SQL &nbsp;](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Funkcja zabezpieczeń bazy danych, która szyfruje magazyn całej bazy danych. |
| [Inspekcja Azure SQL Database](/azure/sql-database/sql-database-auditing)|Funkcja inspekcji bazy danych, która śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.  |


## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem
|Usługa|Opis|
|------|--------|
| [&nbsp; &nbsp; Access Control oparte na rolach na platformie Azure &nbsp;](/azure/active-directory/role-based-access-control-configure)|Funkcja kontroli dostępu zaprojektowana tak, aby umożliwić użytkownikom dostęp tylko do tych zasobów, na podstawie ich ról w organizacji.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Oparte na chmurze repozytorium uwierzytelniania obsługujące wiele dzierżawców, opartych na chmurze katalogów i wielu usług zarządzania tożsamościami na platformie Azure.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Usługa zarządzania tożsamościami, która umożliwia kontrolę nad sposobem tworzenia kont i logowania klientów oraz zarządzania ich profilami w przypadku korzystania z aplikacji opartych na platformie Azure.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| Oparta na chmurze i zarządzana wersja Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)| Inicjowanie obsługi administracyjnej, która wykorzystuje kilka różnych form uwierzytelniania i weryfikacji przed zezwoleniem na dostęp do zabezpieczonych informacji. |

## <a name="backup-and-disaster-recovery"></a>Tworzenie kopii zapasowych i odzyskiwanie po awarii
|Usługa|Opis|
|------|--------|
| [&nbsp;Kopia zapasowa Azure](/azure/backup/backup-introduction-to-azure-backup)| Usługa oparta na platformie Azure służąca do tworzenia kopii zapasowych i przywracania danych w chmurze platformy Azure. |
| [Usługa Azure &nbsp; site &nbsp; Recovery](/azure/site-recovery/site-recovery-overview)|Usługa online, która replikuje obciążenia uruchomione na maszynach fizycznych i wirtualnych z lokacji głównej do pomocniczej lokalizacji, aby umożliwić odzyskanie usług po awarii. |

## <a name="networking"></a>Networking
|Usługa|Opis|
|------|--------|
| [Sieciowe &nbsp; &nbsp; grupy zabezpieczeń](/azure/virtual-network/virtual-networks-nsg)| Funkcja kontroli dostępu opartej na sieci przy użyciu krotki 5, aby podejmować decyzje dotyczące zezwalania lub odmowy.  |
| [Azure VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Urządzenie sieciowe używane jako punkt końcowy sieci VPN, aby umożliwić dostęp między różnymi lokalizacjami do sieci wirtualnych platformy Azure.  |
| [Usługa Azure Application Gateway](/azure/application-gateway/application-gateway-introduction)|Zaawansowany moduł równoważenia obciążenia aplikacji sieci Web, który może kierować na podstawie adresu URL i przeciążać protokół SSL. |
|[Zapora aplikacji sieci Web](/azure/frontdoor/waf-overview) (WAF)|Funkcja Application Gateway, która zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach|
| [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)|Moduł równoważenia obciążenia sieci aplikacji TCP/UDP. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Dedykowany link sieci WAN między sieciami lokalnymi i sieciami wirtualnymi platformy Azure. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| Globalny moduł równoważenia obciążenia DNS.|
| [Serwer proxy aplikacji platformy Azure](/azure/active-directory/active-directory-application-proxy-get-started)| Uwierzytelniający fronton używany do zabezpieczania dostępu zdalnego aplikacji sieci Web hostowanych lokalnie. |
|[Azure Firewall](/azure/firewall/overview)|Zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby Virtual Network platformy Azure.|
|[Azure DDoS Protection](/azure/virtual-network/ddos-protection-overview)|W połączeniu z najlepszymi rozwiązaniami dotyczącymi projektowania aplikacji program zapewnia ochronę przed atakami DDoS.|
|[Punkty końcowe usługi Virtual Network](/azure/virtual-network/virtual-network-service-endpoints-overview)|Rozszerza prywatną przestrzeń adresową sieci wirtualnej i tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia.|