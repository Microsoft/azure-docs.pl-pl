---
title: Jak generować i przesyłać klucze chronione przez moduł HSM dla usługi Azure Key Vault — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Ten artykuł ułatwia planowanie, generowanie, a następnie przenoszenie własnych kluczy chronionych przez moduł HSM do użycia w usłudze Azure Key Vault. Znany również jako BYOK lub przynieść własny klucz.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 28f066668d580f16d831371f2d02a5abcc0e84b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429735"
---
# <a name="import-hsm-protected-keys-for-key-vault-legacy"></a>Importowanie kluczy chronionych przez moduł HSM dla usługi Key Vault (starsza wersja)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby uzyskać dodatkową pewność, korzystając z usługi Azure Key Vault, można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM), które nigdy nie opuszczają granicy modułu HSM. Ten scenariusz jest często określany jako *używania własnego klucza* (BYOK). Usługa Azure Key Vault używa nCipher nShield rodziny modułów HSM (FIPS 140-2 Poziom 2 zatwierdzone) do ochrony kluczy.

Użyj informacji w tym temacie, aby ułatwić planowanie, generowanie, a następnie przenoszenie własnych kluczy chronionych przez moduł HSM do użycia z usługą Azure Key Vault.

Ta funkcja nie jest dostępna dla platformy Azure w Chinach.

> [!NOTE]
> Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../general/overview.md)  
> Aby zapoznać się z samouczkiem wprowadzenie, który obejmuje tworzenie magazynu kluczy chronionych przez moduł HSM, zobacz [Co to jest usługa Azure Key Vault?](../general/overview.md).

Więcej informacji na temat generowania i przesyłania klucza chronionego przez moduł HSM przez Internet:

* Klucz jest generowany ze stacji roboczej w trybie offline, co zmniejsza powierzchnię ataku.
* Klucz jest szyfrowany za pomocą klucza programu CEK (Key Exchange Key,), który pozostaje zaszyfrowany, dopóki nie zostanie przeniesiony do modułów HSM usługi Azure Key Vault. Tylko zaszyfrowana wersja klucza opuszcza oryginalną stację roboczą.
* Zestaw narzędzi ustawia właściwości klucza dzierżawy, który wiąże klucz ze światem zabezpieczeń usługi Azure Key Vault. Więc po azure key vault hsms odbierać i odszyfrowywać klucz, tylko te moduły HSM można go używać. Nie można wyeksportować klucza. To powiązanie jest wymuszane przez nCipher HSMs.
* The Key Exchange Key (KEK) that is used to encrypt your key is generated inside the Azure Key Vault HSMs and is not exportable. Sprzętowe moduły zabezpieczeń wymagają, aby poza ich obrębem nie był dostępny żaden jasny obraz klucza wymiany klucza (KEK). Ponadto zestaw narzędzi zawiera zaświadczenie z nCipher, że KEK nie jest eksportowany i został wygenerowany wewnątrz oryginalnego modułu HSM, który został wyprodukowany przez nCipher.
* Zestaw narzędzi zawiera zaświadczenie z nCipher, że świat zabezpieczeń usługi Azure Key Vault został również wygenerowany na oryginalnym mechanizmie HSM wyprodukowanym przez nCipher. To zaświadczenie dowodzi, że firma Microsoft używa oryginalnego sprzętu.
* Firma Microsoft używa oddzielnych kek i oddzielnych światów zabezpieczeń w każdym regionie geograficznym. Ta separacja gwarantuje, że klucz może być używany tylko w centrach danych w regionie, w którym został zaszyfrowany. Na przykład klucz od klienta europejskiego nie może być używany w centrach danych w Ameryce Północnej lub Azji.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Więcej informacji o modułach HSM i usługach firmy Microsoft

nCipher Security, firma Entrust Datacard, jest liderem na rynku HSM ogólnego przeznaczenia, wzmacniając wiodące na świecie organizacje, zapewniając zaufanie, integralność i kontrolę swoim krytycznym informacjom i aplikacjom biznesowym. Rozwiązania kryptograficzne nCipher zabezpieczają nowe technologie – chmurę, IoT, blockchain, płatności cyfrowe – i pomagają sprostać nowym wymaganiom zgodności, wykorzystując tę samą sprawdzoną technologię, od których globalne organizacje polegają obecnie, aby chronić przed zagrożeniami dla ich poufnych danych, komunikacji sieciowej i infrastruktury korporacyjnej. nCipher zapewnia zaufanie do krytycznych aplikacji biznesowych, zapewniając integralność danych i zapewniając klientom pełną kontrolę – dziś, jutro, przez cały czas.

Firma Microsoft współpracowała z nCipher Security w celu poprawy stanu techniki dla modułów HSM. Opracowane udoskonalenia pozwalają użytkownikom czerpać korzyści z typowych zalet usług hostowanych bez potrzeby rezygnacji z kontroli nad kluczami. Ulepszenia te umożliwiają firmie Microsoft w szczególności zarządzanie sprzętowymi modułami zabezpieczeń, zdejmując ten ciężar z użytkownika. Jako usługa w chmurze usługa Azure Key Vault skaluje się w krótkim czasie, aby sprostać skokom użycia w organizacji. W tym samym czasie klucz jest chroniony wewnątrz modułów HSM firmy Microsoft: zachowujesz kontrolę nad cyklem życia klucza, ponieważ generujesz klucz i przenosisz go do modułów HSM firmy Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementowanie używania własnego klucza (BYOK) na potrzeby usługi Azure Key Vault

Użyj następujących informacji i procedur, jeśli wygenerujesz własny klucz chroniony przez moduł HSM, a następnie przeniesiesz go do usługi Azure Key Vault — scenariusz bring your own key (BYOK).

## <a name="prerequisites-for-byok"></a>Wymagania wstępne dotyczące funkcji BYOK

Zobacz poniższą tabelę, aby uzyskać listę wymagań wstępnych do wprowadzenia własnego klucza (BYOK) dla usługi Azure Key Vault.

| Wymaganie | Więcej informacji |
| --- | --- |
| Subskrypcja platformy Azure |Aby utworzyć usługę Azure Key Vault, potrzebujesz subskrypcji platformy Azure: [Zarejestruj się, aby uzyskać bezpłatną wersję próbną](https://azure.microsoft.com/pricing/free-trial/) |
| Warstwa usługi Azure Key Vault Premium do obsługi kluczy chronionych przez moduł HSM |Aby uzyskać więcej informacji na temat warstw usług i możliwości usługi Azure Key Vault, zobacz [witrynę cen usługi Azure Key Vault.](https://azure.microsoft.com/pricing/details/key-vault/) |
| nCipher nShield HSM, karty inteligentne i oprogramowanie pomocnicze |Musisz mieć dostęp do modułu zabezpieczeń nCipher Hardware i podstawową wiedzę operacyjną nCipher nShield HSM. Zobacz [nCipher nShield Hardware Security Module](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) dla listy kompatybilnych modeli lub zakup modułu HSM, jeśli go nie masz. |
| Następujący sprzęt i oprogramowanie:<ol><li>Stacja robocza x64 w trybie offline z minimalnym systemem operacyjnym Windows 7 i nCipher nShield, który jest co najmniej w wersji 11.50.<br/><br/>Jeśli na tej stacji roboczej jest zainstalowany system Windows 7, należy [zainstalować program Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Stacja robocza, która jest połączona z Internetem i ma zainstalowany minimalny system operacyjny Windows systemu Windows 7 i [azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **w minimalnej wersji 1.1.0.**</li><li>Dysk USB lub inne przenośne urządzenie pamięci masowej z co najmniej 16 MB wolnego miejsca.</li></ol> |Ze względów bezpieczeństwa odradza się podłączanie pierwszej stacji roboczej do sieci. Jednak to zalecenie nie jest programowo egzekwowane.<br/><br/>W poniższych instrukcjach ta stacja robocza jest określana jako odłączona stacja robocza.</p></blockquote><br/>Ponadto jeśli klucz dzierżawy jest dla sieci produkcyjnej, zaleca się użycie drugiej, oddzielnej stacji roboczej, aby pobrać zestaw narzędzi i przekazać klucz dzierżawy. Do celów testowych można jednak użyć pierwszej stacji roboczej.<br/><br/>W poniższych instrukcjach ta druga stacja robocza jest określana jako stacja robocza połączona z Internetem.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generowanie i przenoszenie klucza do modułu HSM usługi Azure Key Vault

Użyjesz następujących pięciu kroków, aby wygenerować i przenieść klucz do modułu HSM usługi Azure Key Vault:

* [Krok 1: Przygotowanie stacji roboczej podłączonej do Internetu](#step-1-prepare-your-internet-connected-workstation)
* [Krok 2: Przygotuj odłączony stację roboczą](#step-2-prepare-your-disconnected-workstation)
* [Krok 3: Wygeneruj klucz](#step-3-generate-your-key)
* [Krok 4. Przygotowanie klucza do przesłania](#step-4-prepare-your-key-for-transfer)
* [Krok 5. Przesłanie klucza do usługi Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Krok 1: Przygotowanie stacji roboczej podłączonej do Internetu

W tym pierwszym kroku wykonaj następujące procedury na stacji roboczej, która jest połączona z Internetem.

### <a name="step-11-install-azure-powershell"></a>Krok 1.1: Instalowanie programu Azure PowerShell

Z połączonej ze Internetem stacji roboczej pobierz i zainstaluj moduł programu Azure PowerShell, który zawiera polecenia cmdlet do zarządzania usługą Azure Key Vault. Aby uzyskać instrukcje instalacji, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Krok 1.2: Uzyskaj identyfikator subskrypcji platformy Azure

Uruchom sesję programu Azure PowerShell i zaloguj się do konta platformy Azure za pomocą następującego polecenia:

```Powershell
   Connect-AzAccount
```
W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Następnie użyj polecenia [Get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

```powershell
   Get-AzSubscription
```
Z danych wyjściowych znajdź identyfikator subskrypcji, która będzie używana dla usługi Azure Key Vault. Ten identyfikator subskrypcji będzie potrzebny później.

Nie należy zamykać okna programu Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Krok 1.3: Pobierz zestaw narzędzi BYOK dla usługi Azure Key Vault

Przejdź do Centrum pobierania firmy Microsoft i [pobierz zestaw narzędzi Azure Key Vault BYOK](https://www.microsoft.com/download/details.aspx?id=45345) dla swojego regionu geograficznego lub wystąpienia platformy Azure. Użyj następujących informacji, aby zidentyfikować nazwę pakietu do pobrania i odpowiadający mu skrót pakietu SHA-256:

---
**Stany Zjednoczone:**

KeyVault-BYOK-Tools-Stany Zjednoczone.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Europie:**

KeyVault-BYOK-Tools-Europe.zip

9AA63E2E7f20CF9BB62485868754203721D2F88D300910634A32DFA1fB19E4A

---
**Azji:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Ameryka Łacińska:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A53892DD1B01A4FACB619

---
**Japonia:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Republika Południowej Afryki:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**Zea:**

KeyVault-BYOK-Tools-ZEA.zip

FADE80210B06962AAa0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Australia:**

KeyVault-BYOK-Tools-Polska.zip

CD0FB7365053DEF8C35116D7C92D203C64a3D3EE2452a025223EEB166901C40A

---
[**Azure dla instytucji rządowych:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**Rząd STANÓW Zjednoczonych DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Niemcy:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AFC02AFD9841F7BADD025D7EE819894A29ED3C71C3F844C45D6

---
**Niemcy Publiczne:**

KeyVault-BYOK-Tools-Niemcy-Public.zip

54534936D0A0C99C8117DB724C34A5E5E50FD204CFCBD75C78972B785865364A29

---
**Indie:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CBA77641134F61B0E0940121C436C8

---
**Francja:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8F8FE7DF

---
**Wielka Brytania:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Szwajcaria:**

KeyVault-BYOK-Tools-Szwajcaria.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


Aby sprawdzić integralność pobranego zestawu narzędzi BYOK, z sesji programu Azure PowerShell należy użyć polecenia cmdlet [Get-FileHash.](https://technet.microsoft.com/library/dn520872.aspx)

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Zestaw narzędzi zawiera:

* Pakiet klucza wymiany klucza (KEK), który ma nazwę zaczynasię od **BYOK-KEK-pkg-.**
* Pakiet Security World, który ma nazwę zaczynasię od **BYOK-SecurityWorld-pkg-.**
* Skrypt języka python o nazwie **verifykeypackage.py.**
* Plik wykonywalny wiersza polecenia o nazwie **KeyTransferRemote.exe** i skojarzone biblioteki DLL.
* Pakiet redystrybucyjny visual C+++ o nazwie **vcredist_x64.exe.**

Skopiuj pakiet na dysk USB lub do innego przenośnego urządzenia pamięci masowej.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Krok 2: Przygotuj odłączony stację roboczą

W tym drugim kroku wykonaj następujące procedury na stacji roboczej, która nie jest połączona z siecią (Internetem lub siecią wewnętrzną).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Krok 2.1: Przygotuj odłączoną stację roboczą za pomocą nCipher nShield HSM

Zainstaluj oprogramowanie obsługi nCipher na komputerze z systemem Windows, a następnie podłącz nCipher nShield HSM do tego komputera.

Upewnij się, że narzędzia nCipher znajdują się na ścieżce (**%nfast_home%\bin**). Na przykład wpisz następujące polecenie:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Aby uzyskać więcej informacji, zobacz podręcznik użytkownika dołączony do modułu HSM nShield.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Krok 2.2: Zainstaluj zestaw narzędzi BYOK na odłączanym stanowisku roboczym

Skopiuj pakiet zestawu narzędzi BYOK z dysku USB lub innego przenośnego urządzenia pamięci masowej, a następnie wykonaj następujące czynności:

1. Wyodrębnij pliki z pobranego pakietu do dowolnego folderu.
2. Z poziomu tego folderu uruchom plik vcredist_x64.exe.
3. Postępuj zgodnie z instrukcjami, aby zainstalować składniki środowiska wykonawczego programu Visual C++ dla programu Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Krok 3: Wygeneruj klucz

W tym trzecim kroku wykonaj następujące procedury na odłączonyej stacji roboczej. Aby wykonać ten krok moduł HSM musi być w trybie inicjowania. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Krok 3.1: Zmień tryb HSM na "I"

Jeśli używasz nCipher nShield Edge, aby zmienić tryb: 1. Użyj przycisku Tryb, aby wyróżnić wymagany tryb. 2. W ciągu kilku sekund naciśnij i przytrzymaj przycisk Wyczyść przez kilka sekund. Jeśli tryb się zmieni, dioda LED nowego trybu przestanie migać i świeci. Dioda LED stanu może migać nieregularnie przez kilka sekund, a następnie miga regularnie, gdy urządzenie jest gotowe. W przeciwnym razie urządzenie pozostaje w trybie bieżącym, z zapaloną diodą LED w odpowiednim trybie.

### <a name="step-32-create-a-security-world"></a>Krok 3.2: Tworzenie świata bezpieczeństwa

Uruchom wiersz polecenia i uruchom program nCipher new-world.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Uruchomienie programu powoduje utworzenie pliku **Security World** w lokalizacji %NFAST_KMDATA%\local\world, co odpowiada lokalizacji folderu C:\ProgramData\nCipher\Key Management Data\local. Możesz użyć różnych wartości kworum, ale w naszym przykładzie zostanie wyświetlony monit o wprowadzenie trzech pustych kart i pinów dla każdej z nich. Następnie, wszystkie dwie karty dają pełny dostęp do świata bezpieczeństwa. Karty te tworzą od tej chwili **zestaw kart administratora** nowego środowiska zabezpieczeń Security World.

> [!NOTE]
> Jeśli moduł HSM nie obsługuje nowszego pakietu cypher DLf3072s256mRijndael, możesz zastąpić --cipher-suite= DLf3072s256mRijndael --cipher-suite=DLf1024s160mRijndael
> 
> Świat bezpieczeństwa stworzony z new-world.exe, który jest dostarczany z oprogramowaniem nCipher w wersji 12.50, nie jest zgodny z tą procedurą BYOK. Dostępne są dwie opcje:
> 1) Downgrade nCipher wersji oprogramowania do 12.40.2, aby utworzyć nowy świat bezpieczeństwa.
> 2) Skontaktuj się z pomocą techniczną nCipher i poproś o podanie poprawki dla wersji oprogramowania 12.50, która pozwala na korzystanie z wersji 12.40.2 new-world.exe, która jest zgodna z tą procedurą BYOK.

Następnie wykonaj poniższe czynności:

* Utwórz kopię zapasową pliku środowiska zabezpieczeń. Zabezpiecz i chroń plik środowiska zabezpieczeń, karty administratora oraz ich kody PIN, a także upewnij się, że nikt nie ma dostępu do więcej niż jednej karty.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Krok 3.3: Zmień tryb HSM na "O"

Jeśli używasz nCipher nShield Edge, aby zmienić tryb: 1. Użyj przycisku Tryb, aby wyróżnić wymagany tryb. 2. W ciągu kilku sekund naciśnij i przytrzymaj przycisk Wyczyść przez kilka sekund. Jeśli tryb się zmieni, dioda LED nowego trybu przestanie migać i świeci. Dioda LED stanu może migać nieregularnie przez kilka sekund, a następnie miga regularnie, gdy urządzenie jest gotowe. W przeciwnym razie urządzenie pozostaje w trybie bieżącym, z zapaloną diodą LED w odpowiednim trybie.

### <a name="step-34-validate-the-downloaded-package"></a>Krok 3.4: Sprawdź poprawność pobranego pakietu

Ta czynność jest opcjonalna, ale zaleca się jej wykonanie w celu upewnienia się, że:

* Klucz wymiany klucza, który znajduje się w zestawie narzędzi został wygenerowany z oryginalnego nCipher nShield HSM.
* Skrót świata zabezpieczeń, który znajduje się w zestawie narzędzi został wygenerowany w oryginalnym nCipher nShield HSM.
* Klucz wymiany klucza nie może zostać wyeksportowany.

> [!NOTE]
> Aby sprawdzić poprawność pobranego pakietu, moduł HSM musi być podłączony, włączony i musi mieć na nim świat zabezpieczeń (taki jak ten, który właśnie został utworzony).

Aby sprawdzić poprawność pobranego pakietu:

1. Uruchom skrypt verifykeypackage.py, wpisując jedną z następujących opcji, w zależności od regionu geograficznego lub wystąpienia platformy Azure:

   * Dla Ameryki Północnej:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Dla Europy:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Azja:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Dla Ameryki Łacińskiej:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Dla Japonii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Dla Korei:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Dla Republiki Południowej Afryki:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * Dla ZEA:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Dla Australii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * W przypadku [platformy Azure government](https://azure.microsoft.com/features/gov/), która używa wystąpienia platformy Azure dla instytucji rządowych w Stanach Zjednoczonych:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Dla rządu USA DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Dla Kanady:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Dla Niemiec:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Dla Niemiec Publicznych:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Dla Indii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Dla Francji:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Dla Zjednoczonego Królestwa:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * Dla Szwajcarii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > Oprogramowanie nCipher nShield zawiera pythona w %NFAST_HOME%\python\bin
     >
     >
2. Upewnij się, że widzisz następujące, co wskazuje na pomyślne sprawdzenie poprawności: **Wynik: SUKCES**

Ten skrypt sprawdza poprawność łańcucha sygnatariusza do klucza głównego nShield. Skrót klucza głównego jest osadzony w skrypcie; jego wartość powinna wynosić **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Możesz również potwierdzić tę wartość oddzielnie, odwiedzając [stronę internetową nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation).

Teraz możesz utworzyć nowy klucz.

### <a name="step-35-create-a-new-key"></a>Krok 3.5: Tworzenie nowego klucza

Wygeneruj klucz za pomocą programu nCipher nShield **generatekey.**

Uruchom następujące polecenie, aby wygenerować klucz:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Podczas uruchamiania tego polecenia należy zastosować się do następujących instrukcji:

* Parametr *protect* musi mieć ustawioną wartość **module**, jak przedstawiono. Spowoduje to utworzenie klucza chronionego przez moduł. Zestaw narzędzi BYOK nie obsługuje kluczy chronionych z użyciem protokołu OCS.
* Zamień wartość *contosokey* dla pozycji **ident** i **plainname** na dowolną wartość ciągu. Aby zminimalizować koszty administracyjne i zmniejszyć ryzyko błędów, zaleca się użycie tej samej wartości dla obu. Wartość **ident** musi zawierać tylko liczby, myślniki i małe litery.
* W tym przykładzie parametr pubexp został pozostawiony pusty (wartość domyślna), można jednak określić konkretne jego wartości. Aby uzyskać więcej informacji, zobacz [nCipher dokumentacji.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

To polecenie tworzy plik klucza tokenizowanego w folderze %NFAST_KMDATA%\local o nazwie zaczyna ącej **key_simple_,** po której następuje **identyfikator** określony w poleceniu. Na przykład: **key_simple_contosokey**. Ten plik zawiera zaszyfrowany klucz.

Utwórz w bezpiecznej lokalizacji kopię zapasową tego pliku stokenizowanego klucza.

> [!IMPORTANT]
> Po późniejszym przeniesieniu klucza do usługi Azure Key Vault firma Microsoft nie może wyeksportować tego klucza z powrotem do Ciebie, więc niezwykle ważne staje się bezpieczne utworzenie kopii zapasowej klucza i świata zabezpieczeń. Skontaktuj się z [nCipher, aby](https://www.ncipher.com/about-us/contact-us) uzyskać wskazówki i najlepsze rozwiązania dotyczące tworzenia kopii zapasowych klucza.
>


Teraz możesz przystąpić do przenoszenia klucza do usługi Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Krok 4. Przygotowanie klucza do przesłania

W tym czwartym kroku wykonaj następujące procedury na odłączonyej stacji roboczej.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Krok 4.1: Utwórz kopię klucza z obniżonymi uprawnieniami

Otwórz nowy wiersz polecenia i zmień bieżący katalog na lokalizację, w której rozpakowano plik zip BYOK. Aby zmniejszyć uprawnienia do klucza, w wierszu polecenia uruchom jedną z następujących czynności, w zależności od regionu geograficznego lub wystąpienia platformy Azure:

* Dla Ameryki Północnej:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Dla Europy:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Azja:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Dla Ameryki Łacińskiej:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Dla Japonii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Dla Korei:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Dla Republiki Południowej Afryki:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* Dla ZEA:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Dla Australii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* W przypadku [platformy Azure government](https://azure.microsoft.com/features/gov/), która używa wystąpienia platformy Azure dla instytucji rządowych w Stanach Zjednoczonych:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Dla rządu USA DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Dla Kanady:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Dla Niemiec:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Dla Niemiec Publicznych:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Dla Indii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Dla Francji:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Dla Zjednoczonego Królestwa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Dla Szwajcarii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

Po uruchomieniu tego polecenia zastąp *contosokey* taką samą wartością, o której została określona w **kroku 3.5: Utwórz nowy klucz** z kroku [Generuj klucz.](#step-3-generate-your-key)

Zostaniesz poproszony o podłączenie kart administratora świata zabezpieczeń.

Po zakończeniu polecenia zostanie wyświetlony **wynik: SUKCES** i kopia klucza o zmniejszonych uprawnieniach\<znajdują się w pliku o nazwie key_xferacId_ contosokey>.

Można sprawdzić ACLS za pomocą następujących poleceń za pomocą nCipher nShield narzędzia:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Po uruchomieniu tych poleceń zastąp contosokey tą samą **wartością określoną w kroku 3.5: Utwórz nowy klucz** z kroku [Generuj klucz.](#step-3-generate-your-key)

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Krok 4.2: Szyfruj klucz za pomocą klucza microsoftu Key Exchange Key Key

Uruchom jedno z następujących poleceń, w zależności od regionu geograficznego lub wystąpienia platformy Azure:

* Dla Ameryki Północnej:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Europy:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Azja:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Ameryki Łacińskiej:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Japonii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Korei:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Republiki Południowej Afryki:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla ZEA:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Australii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* W przypadku [platformy Azure government](https://azure.microsoft.com/features/gov/), która używa wystąpienia platformy Azure dla instytucji rządowych w Stanach Zjednoczonych:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla rządu USA DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Kanady:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Niemiec:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Niemiec Publicznych:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Indii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Francji:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Zjednoczonego Królestwa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Dla Szwajcarii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Podczas uruchamiania tego polecenia należy zastosować się do następujących instrukcji:

* Zastąp *contosokey* identyfikatorem używanym do generowania klucza w **kroku 3.5: Utwórz nowy klucz** z kroku [Generuj klucz.](#step-3-generate-your-key)
* Zamień *identyfikator subskrypcji* na identyfikator subskrypcji platformy Azure, który zawiera magazyn kluczy. Pobrano tę wartość wcześniej, w **kroku 1.2: Pobierz identyfikator subskrypcji platformy Azure** z kroku Przygotowanie stacji [roboczej połączonej z Internetem.](#step-1-prepare-your-internet-connected-workstation)
* Zastąp *ContosoFirstHSMKey* etykietą używaną dla nazwy pliku wyjściowego.

Po pomyślnym zakończeniu wyświetla **wynik: SUKCES** i w bieżącym folderze znajduje się nowy plik o następującej nazwie: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Krok 4.3: Skopiuj pakiet transferu kluczy do stacji roboczej podłączonej do Internetu

Użyj dysku USB lub innego przenośnego magazynu, aby skopiować plik wyjściowy z poprzedniego kroku (KeyTransferPackage-ContosoFirstHSMkey.byok) do stacji roboczej podłączonej do Internetu.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Krok 5. Przesłanie klucza do usługi Azure Key Vault

W tym ostatnim kroku na stacji roboczej połączonej z Internetem użyj polecenia cmdlet [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/add-azkeyvaultkey) aby przekazać pakiet transferu kluczy skopiowany z odłączonej stacji roboczej do modułu HSM usługi Azure Key Vault:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Jeśli przekazywanie zakończy się pomyślnie, wyświetlane są właściwości właśnie dodanego klucza.

## <a name="next-steps"></a>Następne kroki

Teraz można użyć tego klucza chronionego przez moduł HSM w magazynie kluczy. Aby uzyskać więcej informacji, zobacz porównanie cen i [funkcji](https://azure.microsoft.com/pricing/details/key-vault/).
