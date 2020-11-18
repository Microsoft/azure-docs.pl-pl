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
ms.openlocfilehash: 553cfe0c1a5e7d569c127f23a11f25f4d150b0bc
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700763"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Usługi i technologie zabezpieczeń dostępne na platformie Azure

W naszych dyskusjach z bieżącymi i przyszłymi klientami platformy Azure często pytamy "czy masz listę wszystkich usług związanych z zabezpieczeniami i technologii oferowanych przez platformę Azure?".

Podczas oceniania opcji dostawcy usług w chmurze warto uzyskać te informacje. Ta lista została udostępniona, aby rozpocząć pracę.

Wraz z upływem czasu ta lista ulegnie zmianie i zwiększeniu, podobnie jak w przypadku platformy Azure. Pamiętaj, aby regularnie sprawdzać Tę stronę, aby zachować aktualność w naszych usługach i technologiach związanych z bezpieczeństwem.

## <a name="general-azure-security"></a>Ogólne zabezpieczenia platformy Azure
|Usługa|Opis|
|--------|--------|
|[&nbsp;Centrum zabezpieczeń &nbsp; Azure](../../security-center/security-center-introduction.md)| Rozwiązanie do ochrony obciążeń w chmurze, które zapewnia zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami w ramach obciążeń chmury hybrydowej.|
|[Usługa Azure Key Vault](../../key-vault/general/overview.md)| Bezpieczne przechowywanie wpisów tajnych dla haseł, parametrów połączenia i innych informacji potrzebnych do działania aplikacji. |
|[Dzienniki usługi Azure Monitor](../../azure-monitor/log-query/log-query-overview.md)|Usługa monitorowania, która zbiera dane telemetryczne i inne, oraz udostępnia język zapytań i aparat analityczny do dostarczania usługi Operational Insights dla aplikacji i zasobów. Może być używany samodzielnie lub z innymi usługami, takimi jak Security Center. |
|[Azure Dev/Test Labs](../../devtest-labs/devtest-lab-overview.md)|Usługa, która pomaga deweloperom i testerom szybko tworzyć środowiska na platformie Azure, jednocześnie minimalizując straty i kontrolując koszty.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Zabezpieczenia magazynu
|Usługa|Opis|
|------|--------|
| [&nbsp; &nbsp; Szyfrowanie usługi Azure &nbsp; Storage](../../storage/common/storage-service-encryption.md)|Funkcja zabezpieczeń, która automatycznie szyfruje dane w usłudze Azure Storage.   |
|[StorSimple zaszyfrowanego magazynu hybrydowego](../../storsimple/storsimple-ova-overview.md)| Zintegrowane rozwiązanie magazynu, które zarządza zadaniami magazynu między urządzeniami lokalnymi i magazynem w chmurze platformy Azure.|
|[Szyfrowanie Client-Side platformy Azure](../../storage/common/storage-client-side-encryption.md)| Rozwiązanie do szyfrowania po stronie klienta, które szyfruje dane w aplikacjach klienckich przed przekazaniem ich do usługi Azure Storage; Ponadto odszyfrowuje dane podczas pobierania. |
| [Sygnatury dostępu współdzielonego usługi Azure Storage](../../storage/common/storage-sas-overview.md)|Sygnatura dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu.  |
|[Klucze konta usługi Azure Storage](../../storage/common/storage-account-create.md)| Metoda kontroli dostępu do usługi Azure Storage, która jest używana do uwierzytelniania podczas uzyskiwania dostępu do konta magazynu. |
|[Udziały plików platformy Azure z szyfrowaniem SMB 3,0](../../storage/files/storage-files-introduction.md)|Technologia zabezpieczeń sieci, która umożliwia automatyczne szyfrowanie sieci na potrzeby protokołu udostępniania plików bloku komunikatów serwera (SMB). |
|[analityka magazynu platformy Azure](/rest/api/storageservices/Storage-Analytics)| Rejestrowanie i technologia generowania metryk dla danych na koncie magazynu. |

<!------>

## <a name="database-security"></a>Zabezpieczenia bazy danych
|Usługa|Opis|
|------|--------|
| [&nbsp;Zapora Azure SQL &nbsp;](../../azure-sql/database/firewall-configure.md)|Funkcja kontroli dostępu do sieci chroniąca przed atakami opartymi na sieci do bazy danych programu. |
|[&nbsp;Szyfrowanie na &nbsp; poziomie komórek SQL &nbsp; Azure](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database)| Technologia zabezpieczeń bazy danych, która zapewnia szyfrowanie na poziomie szczegółowości.  |
| [&nbsp; &nbsp; Szyfrowanie połączenia usługi Azure SQL](../../azure-sql/database/logins-create-manage.md)|Aby zapewnić bezpieczeństwo, usługa SQL Database kontroluje dostęp za pomocą reguł zapory, ograniczając adresy IP dla połączeń, stosując mechanizmy uwierzytelniania wymagające potwierdzenia tożsamości przez użytkowników oraz mechanizmy autoryzacji ograniczające użytkowników do określonych działań i danych. |
| [Zawsze szyfrowanie w usłudze Azure SQL](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Chroni dane poufne, takie jak numery kart kredytowych lub krajowe numery identyfikacyjne (na przykład numery ubezpieczenia społecznego w Stanach Zjednoczonych), przechowywane w bazie danych Azure SQL Database lub SQL Server.  |
| [&nbsp;Transparent Data Encryption Azure SQL &nbsp;](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Funkcja zabezpieczeń bazy danych, która szyfruje magazyn całej bazy danych. |
| [Inspekcja Azure SQL Database](../../azure-sql/database/auditing-overview.md)|Funkcja inspekcji bazy danych, która śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie usługi Azure Storage.  |


## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem
|Usługa|Opis|
|------|--------|
| [&nbsp;Kontrola dostępu oparta na rolach na platformie Azure &nbsp;](../../role-based-access-control/role-assignments-portal.md)|Funkcja kontroli dostępu zaprojektowana tak, aby umożliwić użytkownikom dostęp tylko do tych zasobów, na podstawie ich ról w organizacji.  |
| [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)|Oparte na chmurze repozytorium uwierzytelniania obsługujące wiele dzierżawców, opartych na chmurze katalogów i wielu usług zarządzania tożsamościami na platformie Azure.  |
| [Azure Active Directory B2C](../../active-directory-b2c/overview.md)|Usługa zarządzania tożsamościami, która umożliwia kontrolę nad sposobem tworzenia kont i logowania klientów oraz zarządzania ich profilami w przypadku korzystania z aplikacji opartych na platformie Azure.   |
| [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)| Oparta na chmurze i zarządzana wersja Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)| Inicjowanie obsługi administracyjnej, która wykorzystuje kilka różnych form uwierzytelniania i weryfikacji przed zezwoleniem na dostęp do zabezpieczonych informacji. |

## <a name="backup-and-disaster-recovery"></a>Tworzenie kopii zapasowych i odzyskiwanie po awarii
|Usługa|Opis|
|------|--------|
| [&nbsp;Kopia zapasowa Azure](../../backup/backup-overview.md)| Usługa oparta na platformie Azure służąca do tworzenia kopii zapasowych i przywracania danych w chmurze platformy Azure. |
| [Usługa Azure &nbsp; site &nbsp; Recovery](../../site-recovery/site-recovery-overview.md)|Usługa online, która replikuje obciążenia uruchomione na maszynach fizycznych i wirtualnych z lokacji głównej do pomocniczej lokalizacji, aby umożliwić odzyskanie usług po awarii. |

## <a name="networking"></a>Sieć
|Usługa|Opis|
|------|--------|
| [Sieciowe &nbsp; &nbsp; grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md)| Funkcja kontroli dostępu opartej na sieci przy użyciu krotki 5, aby podejmować decyzje dotyczące zezwalania lub odmowy.  |
| [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)| Urządzenie sieciowe używane jako punkt końcowy sieci VPN, aby umożliwić dostęp między różnymi lokalizacjami do sieci wirtualnych platformy Azure.  |
| [Usługa Azure Application Gateway](../../application-gateway/overview.md)|Zaawansowany moduł równoważenia obciążenia aplikacji sieci Web, który może kierować na podstawie adresu URL i przeciążać protokół SSL. |
|[Zapora aplikacji sieci Web](../../web-application-firewall/afds/afds-overview.md) (WAF)|Funkcja Application Gateway, która zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach|
| [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)|Moduł równoważenia obciążenia sieci aplikacji TCP/UDP. |
| [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)| Dedykowany link sieci WAN między sieciami lokalnymi i sieciami wirtualnymi platformy Azure. |
| [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)| Globalny moduł równoważenia obciążenia DNS.|
| [Serwer proxy aplikacji platformy Azure](../../active-directory/manage-apps/application-proxy.md)| Uwierzytelniający fronton używany do zabezpieczania dostępu zdalnego aplikacji sieci Web hostowanych lokalnie. |
|[Azure Firewall](../../firewall/overview.md)|Zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby Virtual Network platformy Azure.|
|[Azure DDoS Protection](../../virtual-network/ddos-protection-overview.md)|W połączeniu z najlepszymi rozwiązaniami dotyczącymi projektowania aplikacji program zapewnia ochronę przed atakami DDoS.|
|[Punkty końcowe usługi Virtual Network](../../virtual-network/virtual-network-service-endpoints-overview.md)|Rozszerza prywatną przestrzeń adresową sieci wirtualnej i tożsamość sieci wirtualnej do usług platformy Azure za pośrednictwem bezpośredniego połączenia.|