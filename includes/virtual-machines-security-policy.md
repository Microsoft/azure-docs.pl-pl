---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d1ec61bf18248ea56c8ee5e430a671af7f39d732
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458674"
---
Ważne jest, aby zachować maszynę wirtualną (VM) bezpieczne dla aplikacji, które można uruchomić. Zabezpieczanie maszyn wirtualnych może obejmować jedną lub więcej usług platformy Azure i funkcji, które obejmują bezpieczny dostęp do maszyn wirtualnych i bezpieczne przechowywanie danych. Ten artykuł zawiera informacje, które umożliwiają bezpieczne dla maszyny Wirtualnej i aplikacji.

## <a name="antimalware"></a>Antimalware

Nowoczesny krajobraz zagrożeń dla środowisk chmurowych jest dynamiczny, zwiększając presję na utrzymanie skutecznej ochrony w celu spełnienia wymagań dotyczących zgodności i zabezpieczeń. [Microsoft Antimalware for Azure](../articles/security/fundamentals/antimalware.md) to bezpłatna funkcja ochrony w czasie rzeczywistym, która pomaga identyfikować i usuwać wirusy, programy szpiegujące i inne złośliwe oprogramowanie. Alerty można skonfigurować tak, aby powiadamiały użytkownika, gdy znane złośliwe lub niechciane oprogramowanie próbuje zainstalować się lub uruchomić na maszynie wirtualnej. Nie jest obsługiwana na maszynach wirtualnych z systemem Linux lub Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Usługa Azure Security Center](../articles/security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom na maszynach wirtualnych, wykrywać i reagować na nie. Usługa Security Center zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby przejść niezauważone, i współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Dostęp just-in-time usługi Security Center można zastosować we wdrożeniu maszyny Wirtualnej, aby zablokować ruch przychodzący do maszyn wirtualnych platformy Azure, zmniejszając narażenie na ataki, zapewniając jednocześnie łatwy dostęp do łączenia się z maszynami wirtualnymi w razie potrzeby. Gdy opcja just-in-time jest włączona, a użytkownik żąda dostępu do maszyny Wirtualnej, Usługa Security Center sprawdza, jakie uprawnienia ma użytkownik dla maszyny Wirtualnej. Jeśli mają odpowiednie uprawnienia, żądanie zostanie zatwierdzone, a Usługa Security Center automatycznie konfiguruje sieciowe grupy zabezpieczeń (NSG), aby zezwalać na ruch przychodzący do wybranych portów przez ograniczony czas. Po upływie tego czasu usługa Security Center przywraca cele zabezpieczeń do poprzednich stanów. 

## <a name="encryption"></a>Szyfrowanie

Dla dysków zarządzanych oferowane są dwie metody szyfrowania. Szyfrowanie na poziomie systemu operacyjnego, który jest szyfrowanie dysków Azure i szyfrowanie na poziomie platformy, która jest szyfrowanie po stronie serwera.

### <a name="server-side-encryption"></a>Szyfrowanie po stronie serwera

Dyski zarządzane platformy Azure domyślnie automatycznie szyfrują dane podczas utrwalania ich w chmurze. Szyfrowanie po stronie serwera chroni dane i pomaga spełnić zobowiązania dotyczące zabezpieczeń i zgodności z przepisami organizacji. Dane na dyskach zarządzanych platformy Azure są szyfrowane w sposób przezroczysty przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych dostępnych szyfrów blokowych i zgodnego ze standardem FIPS 140-2.

Szyfrowanie nie wpływa na wydajność dysków zarządzanych. Szyfrowanie nie wiąże się z żadnymi dodatkowymi kosztami.

Szyfrowanie można polegać na kluczach zarządzanych przez platformę do szyfrowania dysku zarządzanego lub zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli zdecydujesz się zarządzać szyfrowaniem za pomocą własnych kluczy, możesz określić *klucz zarządzany przez klienta* do szyfrowania i odszyfrowywania wszystkich danych na dyskach zarządzanych. 

Aby dowiedzieć się więcej na temat szyfrowania po stronie serwera, zapoznaj się z artykułami dotyczącymi [systemu Windows](../articles/virtual-machines/windows/disk-encryption.md) lub [Linux](../articles/virtual-machines/linux/disk-encryption.md).

### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption

Aby zwiększyć bezpieczeństwo i zgodność maszyn wirtualnych [z systemem Windows](../articles/virtual-machines/windows/disk-encryption-overview.md) i [Linux,](../articles/virtual-machines/linux/disk-encryption-overview.md) dyski wirtualne na platformie Azure mogą być szyfrowane. Dyski wirtualne na maszynach wirtualnych systemu Windows są szyfrowane w spoczynku przy użyciu funkcji BitLocker. Dyski wirtualne na maszynach wirtualnych z systemem Linux są szyfrowane w spoczynku przy użyciu dm-crypt. 

Szyfrowanie dysków wirtualnych na platformie Azure nie jest naliczane. Klucze kryptograficzne są przechowywane w usłudze Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowanych zgodnie ze standardami FIPS 140-2 poziomu 2. Te klucze kryptograficzne są używane do szyfrowania i odszyfrowywania dysków wirtualnych dołączonych do maszyny Wirtualnej. Zachowujesz kontrolę nad tymi kluczami kryptograficznymi i możesz kontrolować ich użycie. Podmiot usługi Azure Active Directory zapewnia bezpieczny mechanizm wydawania tych kluczy kryptograficznych, gdy maszyny wirtualne są włączone i wyłączone.

## <a name="key-vault-and-ssh-keys"></a>Przechowalnia kluczy i klucze SSH

Wpisy tajne i certyfikaty mogą być modelowane jako zasoby i dostarczane przez [magazyn kluczy](../articles/key-vault/key-vault-whatis.md). Za pomocą programu Azure PowerShell można tworzyć magazyny kluczy dla [maszyn wirtualnych z systemem Windows](../articles/virtual-machines/windows/key-vault-setup.md) i maszyn wirtualnych platformy Azure CLI dla systemów [Linux.](../articles/virtual-machines/linux/key-vault-setup.md) Można również utworzyć klucze do szyfrowania.

Zasady dostępu do magazynu kluczy przyznają uprawnienia do kluczy, wpisów tajnych i certyfikatów oddzielnie. Na przykład można udzielić użytkownikowi dostępu tylko do kluczy, ale żadnych uprawnień do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych lub certyfikatów są jednak przyznawane na poziomie magazynu. Innymi słowy [zasady dostępu do magazynu kluczy](../articles/key-vault/key-vault-secure-your-key-vault.md) nie obsługuje uprawnień na poziomie obiektu.

Po nawiązaniu połączenia z maszynami wirtualnymi należy użyć kryptografii klucza publicznego, aby zapewnić bezpieczniejszy sposób logowania się do nich. Proces ten obejmuje wymianę kluczy publicznych i prywatnych przy użyciu polecenia secure shell (SSH) do uwierzytelniania siebie, a nie nazwy użytkownika i hasła. Hasła są narażone na ataki siłowe, szczególnie na maszynach wirtualnych z internetem, takich jak serwery sieci Web. Dzięki parze kluczy bezpiecznej powłoki (SSH) można utworzyć maszynę [wirtualną z systemem Linux,](../articles/virtual-machines/linux/mac-create-ssh-keys.md) która używa kluczy SSH do uwierzytelniania, eliminując konieczność logowania się haseł. Można również użyć kluczy SSH do łączenia się z [maszyny Wirtualnej systemu Windows](../articles/virtual-machines/linux/ssh-from-windows.md) do maszyny Wirtualnej systemu Linux.

## <a name="managed-identities-for-azure-resources"></a>Tożsamości zarządzane dla zasobów platformy Azure

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób zarządzania poświadczeniami w kodzie w przypadku uwierzytelniania przy użyciu usług w chmurze. Zabezpieczanie poświadczeń to ważne zadanie. W idealnej sytuacji poświadczenia nie są nigdy wyświetlane na stacjach roboczych deweloperów ani zaewidencjonowane do kontroli źródła. Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń, wpisów tajnych i innych kluczy, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. 

Funkcja tożsamości zarządzanych dla platformy Azure w usłudze Azure Active Directory (Azure AD) rozwiązuje ten problem. Funkcja ta udostępnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności umieszczania poświadczeń w kodzie.  Kod, który jest uruchomiony na maszynie Wirtualnej można zażądać tokenu z dwóch punktów końcowych, które są dostępne tylko z poziomu maszyny Wirtualnej. Aby uzyskać bardziej szczegółowe informacje na temat tej usługi, przejrzyj zarządzaną stronę omówienia [zasobów platformy Azure.](../articles/active-directory/managed-identities-azure-resources/overview.md)   

## <a name="policies"></a>Zasady

[Zasady platformy Azure](../articles/azure-policy/azure-policy-introduction.md) mogą służyć do definiowania żądanego zachowania maszyn [wirtualnych](../articles/virtual-machines/windows/policy.md) z systemem Windows i [maszyn wirtualnych z systemem Linux](../articles/virtual-machines/linux/policy.md)w organizacji. Korzystając z zasad, organizacja może wymuszać różne konwencje i reguły w całym przedsiębiorstwie. Wymuszanie pożądanego zachowania może pomóc zmniejszyć ryzyko, przyczyniając się jednocześnie do sukcesu organizacji.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Za pomocą [kontroli dostępu opartej na rolach (RBAC)](../articles/role-based-access-control/overview.md)można segregować obowiązki w zespole i przyznać tylko ilość dostępu do użytkowników na maszynie Wirtualnej, które są potrzebne do wykonywania swoich zadań. Zamiast dawać wszystkim nieograniczone uprawnienia na maszynie Wirtualnej, można zezwolić tylko na niektóre akcje. Kontrolę dostępu dla maszyny Wirtualnej można skonfigurować w [witrynie Azure portal](../articles/role-based-access-control/role-assignments-portal.md), używając [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/role)lub programu Azure[PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Następne kroki
- Przejdź przez kroki, aby monitorować bezpieczeństwo maszyny wirtualnej przy użyciu usługi Azure Security Center dla [systemów Linux](../articles/security/fundamentals/overview.md) lub [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).
