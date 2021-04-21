---
title: Zabezpieczanie i używanie zasad
description: Dowiedz się więcej o zabezpieczeniach i zasadach dotyczących maszyn wirtualnych na platformie Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 6754e4e60c31c35531b27e6cc2047e9f8ce3d0bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817399"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Zabezpieczanie maszyn wirtualnych platformy Azure i stosowanie zasad

Ważne jest, aby zapewnić bezpieczeństwo maszyny wirtualnej dla uruchamianych aplikacji. Zabezpieczanie maszyn wirtualnych może obejmować co najmniej jedną usługę i funkcje platformy Azure, które obejmują bezpieczny dostęp do maszyn wirtualnych i bezpieczny magazyn danych. Ten artykuł zawiera informacje, które umożliwiają zachowanie bezpieczeństwa maszyny wirtualnej i aplikacji.

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem

Nowoczesny krajobraz zagrożeń dla środowisk chmurowych jest dynamiczny, co zwiększa nacisk na utrzymanie efektywnej ochrony w celu spełnienia wymagań dotyczących zgodności i zabezpieczeń. [Microsoft Antimalware platformy Azure to](../security/fundamentals/antimalware.md) bezpłatna funkcja ochrony w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie. Alerty można skonfigurować tak, aby powiadamiały o próbach zainstalowania lub uruchomienia na maszynie wirtualnej znanego złośliwego lub niechcianego oprogramowania. Nie jest obsługiwana na maszynach wirtualnych z systemem Linux lub Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-introduction.md) pomaga zapobiegać zagrożeniom maszyn wirtualnych, wykrywać je i reagować na nie. Security Center zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w subskrypcjach platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby zostać niezauwagowane, i współpracuje z szerokim ekosystemem rozwiązań zabezpieczeń.

Security Center dostępu just in time można zastosować we wdrożeniu maszyny wirtualnej, aby zablokować ruch przychodzący do maszyn wirtualnych platformy Azure, zmniejszając narażenie na ataki przy jednoczesnym zapewnieniu łatwego dostępu do łączenia się z maszynami wirtualnym w razie potrzeby. Gdy funkcja just in time jest włączona, a użytkownik żąda dostępu do maszyny wirtualnej, Security Center sprawdza, jakie uprawnienia ma użytkownik dla maszyny wirtualnej. Jeśli użytkownik ma odpowiednie uprawnienia, żądanie zostanie zatwierdzone i program Security Center automatycznie skonfiguruje sieciowe grupy zabezpieczeń, aby zezwolić na ruch przychodzący do wybranych portów przez ograniczony czas. Po upływie tego czasu Security Center sieciowych sieciowych Security Center do ich poprzednich stanów. 

## <a name="encryption"></a>Szyfrowanie

Dla dysków zarządzanych są oferowane dwie metody szyfrowania. Szyfrowanie na poziomie systemu operacyjnego, Azure Disk Encryption, i szyfrowanie na poziomie platformy, czyli szyfrowanie po stronie serwera.

### <a name="server-side-encryption"></a>Szyfrowanie po stronie serwera

Dyski zarządzane platformy Azure domyślnie automatycznie szyfrują dane podczas utrwalania ich w chmurze. Szyfrowanie po stronie serwera chroni dane i pomaga spełnić wymagania organizacji dotyczące zabezpieczeń i zgodności. Dane na dyskach zarządzanych platformy Azure są szyfrowane w sposób przezroczysty przy użyciu 256-bitowego szyfrowania [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych dostępnych szyfrów blokowych i są zgodne ze standardem FIPS 140-2.

Szyfrowanie nie ma wpływu na wydajność dysków zarządzanych. Szyfrowanie nie ma żadnych dodatkowych kosztów.

Do szyfrowania dysku zarządzanego można polegać na kluczach zarządzanych przez platformę lub można zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli zdecydujesz się zarządzać szyfrowaniem przy  użyciu własnych kluczy, możesz określić klucz zarządzany przez klienta, który będzie używany do szyfrowania i odszyfrowywania wszystkich danych na dyskach zarządzanych. 

Aby dowiedzieć się więcej na temat szyfrowania po stronie serwera, zapoznaj się z artykułami dla systemu [Windows](./disk-encryption.md) lub [Linux.](./disk-encryption.md)

### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption

W przypadku zwiększonych [zabezpieczeń i zgodności maszyn](windows/disk-encryption-overview.md) wirtualnych z systemem Windows i [linux](linux/disk-encryption-overview.md) dyski wirtualne na platformie Azure mogą być szyfrowane. Dyski wirtualne na maszynach wirtualnych z systemem Windows są szyfrowane podczas spoczynku przy użyciu funkcji BitLocker. Dyski wirtualne na maszynach wirtualnych z systemem Linux są szyfrowane podczas spoczynku przy użyciu programu dm-crypt. 

Szyfrowanie dysków wirtualnych na platformie Azure jest bezpłatne. Klucze kryptograficzne są przechowywane w programie Azure Key Vault przy użyciu ochrony oprogramowania lub można zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowanych zgodnie ze standardami FIPS 140-2 poziom 2. Te klucze kryptograficzne są używane do szyfrowania i odszyfrowywania dysków wirtualnych dołączonych do maszyny wirtualnej. Zachowujesz kontrolę nad tymi kluczami kryptograficznymi i możesz kontrolować ich użycie. Główna Azure Active Directory zapewnia bezpieczny mechanizm wystawiania tych kluczy kryptograficznych, ponieważ maszyny wirtualne są włączone i wyłączone.

## <a name="key-vault-and-ssh-keys"></a>Key Vault i klucze SSH

Wpisy tajne i certyfikaty mogą być modelowane jako zasoby i udostępniane przez Key Vault [.](../key-vault/general/basic-concepts.md) Usługi azure Azure PowerShell do tworzenia magazynów kluczy dla maszyn wirtualnych z systemem [Windows](windows/key-vault-setup.md) i interfejsu wiersza polecenia platformy Azure dla [maszyn wirtualnych z systemem Linux.](linux/key-vault-setup.md) Można również utworzyć klucze szyfrowania.

Zasady dostępu magazynu kluczy przyznają oddzielnie uprawnienia do kluczy, wpisów tajnych i certyfikatów. Na przykład można udzielić użytkownikowi dostępu tylko do kluczy, ale żadnych uprawnień do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych lub certyfikatów są jednak przyznawane na poziomie magazynu. Innymi słowy, zasady [dostępu magazynu kluczy](../key-vault/general/security-features.md) nie obsługują uprawnień na poziomie obiektu.

Podczas nawiązywania połączenia z maszynami wirtualnych należy użyć kryptografii klucza publicznego, aby zapewnić bezpieczniejszy sposób logowania się do nich. Ten proces obejmuje wymianę kluczy publicznych i prywatnych przy użyciu polecenia Secure Shell (SSH) w celu uwierzytelnienia się, a nie nazwy użytkownika i hasła. Hasła są narażone na ataki siłowe, szczególnie na maszyny wirtualne dostępne z Internetu, takie jak serwery internetowe. Za pomocą pary kluczy Secure Shell (SSH) można utworzyć maszynę wirtualną z systemem [Linux,](linux/mac-create-ssh-keys.md) która używa kluczy SSH do uwierzytelniania, eliminując konieczność logowania się przy użyciu haseł. Możesz również użyć kluczy SSH, aby nawiązać połączenie z maszyny wirtualnej z systemem [Windows](linux/ssh-from-windows.md) do maszyny wirtualnej z systemem Linux.

## <a name="managed-identities-for-azure-resources"></a>Tożsamości zarządzane dla zasobów platformy Azure

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób zarządzania poświadczeniami w kodzie w przypadku uwierzytelniania przy użyciu usług w chmurze. Zabezpieczanie poświadczeń to ważne zadanie. W idealnej sytuacji poświadczenia nie są nigdy wyświetlane na stacjach roboczych deweloperów ani zaewidencjonowane do kontroli źródła. Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń, wpisów tajnych i innych kluczy, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. 

Funkcja tożsamości zarządzanych dla platformy Azure w usłudze Azure Active Directory (Azure AD) rozwiązuje ten problem. Funkcja ta udostępnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności umieszczania poświadczeń w kodzie.  Kod uruchomiony na maszynie wirtualnej może zażądać tokenu z dwóch punktów końcowych, które są dostępne tylko z poziomu maszyny wirtualnej. Aby uzyskać bardziej szczegółowe informacje na temat tej usługi, zapoznaj się ze stroną przeglądu [tożsamości zarządzanych dla zasobów](../active-directory/managed-identities-azure-resources/overview.md) platformy Azure.   

## <a name="policies"></a>Zasady

[Zasady platformy Azure](../governance/policy/overview.md) mogą służyć do definiowania żądanego zachowania dla maszyn wirtualnych z systemem [Windows](./windows/policy.md) i Linux [w organizacji.](./linux/policy.md) Dzięki użyciu zasad organizacja może wymuszać różne konwencje i reguły w całym przedsiębiorstwie. Wymuszanie żądanego zachowania może pomóc ograniczyć ryzyko, jednocześnie wpływając na powodzenie organizacji.

## <a name="azure-role-based-access-control"></a>Kontrola dostępu na podstawie ról na platformie Azure

Korzystając z kontroli dostępu opartej na rolach [(RBAC)](../role-based-access-control/overview.md)platformy Azure, można segregować obowiązki w ramach zespołu i udzielać użytkownikom na maszynie wirtualnej tylko dostępu potrzebnego do wykonywania zadań. Zamiast nadawać wszystkim użytkownikom nieograniczone uprawnienia na maszynie wirtualnej, możesz zezwolić tylko na niektóre akcje. Kontrolę dostępu dla maszyny wirtualnej można skonfigurować w Azure Portal [,](../role-based-access-control/role-assignments-portal.md)przy użyciu interfejsu wiersza polecenia platformy [Azure](/cli/azure/role)[lub Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Następne kroki
- Kroki monitorowania zabezpieczeń maszyny wirtualnej za pomocą usługi Azure Security Center dla [systemu Linux](../security/fundamentals/overview.md) lub [Windows.](/previous-versions/azure/virtual-machines/tutorial-azure-security)