---
title: Transport Layer Security w Azure Backup
description: Dowiedz się, jak włączyć obsługę Azure Backup przy użyciu protokołu szyfrowania Transport Layer Security (TLS), aby zapewnić bezpieczeństwo danych podczas ich przesyłania przez sieć.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: 9881fd3532cbc7d67c7d5adbce75e02fc62e0bcf
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280695"
---
# <a name="transport-layer-security-in-azure-backup"></a>Transport Layer Security w Azure Backup

Transport Layer Security (TLS) to protokół szyfrowania, który umożliwia Zabezpieczanie danych podczas ich przesyłania przez sieć. Azure Backup używa zabezpieczeń warstwy transportu do ochrony prywatności danych kopii zapasowej, które są transferowane. W tym artykule opisano kroki umożliwiające włączenie protokołu TLS 1,2, który zapewnia lepszą ochronę przed poprzednimi wersjami.

## <a name="earlier-versions-of-windows"></a>Wcześniejsze wersje systemu Windows

Jeśli na komputerze jest uruchomionych wcześniejszych wersji systemu Windows, należy zainstalować odpowiednie aktualizacje wymienione poniżej i należy zastosować zmiany w rejestrze udokumentowane w artykułach bazy wiedzy.

|System operacyjny  |Artykuł bazy wiedzy |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>Aktualizacja zainstaluje wymagane składniki protokołu. Po zakończeniu instalacji należy wprowadzić zmiany klucza rejestru wymienione w powyższych artykułach bazy wiedzy, aby prawidłowo włączyć wymagane protokoły.

## <a name="verify-windows-registry"></a>Weryfikuj rejestr systemu Windows

### <a name="configuring-schannel-protocols"></a>Konfigurowanie protokołów SChannel

Następujące klucze rejestru zapewniają włączenie protokołu TLS 1,2 na poziomie składnika SChannel:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>Wyświetlane wartości są domyślnie ustawione w systemie Windows Server 2012 R2 i nowszych wersjach. W przypadku tych wersji systemu Windows, jeśli klucze rejestru nie są obecne, nie trzeba ich tworzyć.

### <a name="configuring-net-framework"></a>Konfigurowanie .NET Framework

Następujące klucze rejestru umożliwiają skonfigurowanie .NET Framework do obsługi mocnej kryptografii. Więcej informacji na temat [konfigurowania .NET Framework](https://docs.microsoft.com/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)można znaleźć tutaj.

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-enable-tls-12"></a>Dlaczego należy włączyć protokół TLS 1,2?

Protokół TLS 1,2 jest bezpieczniejszy niż poprzednie protokoły kryptograficzne, takie jak SSL 2,0, SSL 3,0, TLS 1,0 i TLS 1,1. Usługi Azure Backup już w pełni obsługiwały protokół TLS 1,2.

### <a name="what-determines-the-encryption-protocol-used"></a>Co określa używany protokół szyfrowania?

Największa wersja protokołu obsługiwana przez klienta i serwer jest negocjowana w celu nawiązania zaszyfrowanej konwersacji. Aby uzyskać więcej informacji na temat protokołu uzgadniania TLS, zobacz [Ustanawianie bezpiecznej sesji przy użyciu protokołu TLS](https://docs.microsoft.com/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>Jaki jest wpływ niewłączania protokołu TLS 1,2?

W celu zwiększenia bezpieczeństwa ataków wykorzystujących obniżanie poziomu protokołów Azure Backup zaczyna wyłączyć wersje TLS starsze niż 1,2 w sposób fazowy. Jest to część długoterminowej zmiany między usługami, aby uniemożliwić starsze połączenia protokołów i mechanizmów szyfrowania. Usługi Azure Backup i składniki w pełni obsługują protokół TLS 1,2. Jednak wersje systemu Windows, które nie wymagają aktualizacji lub niektóre dostosowane konfiguracje, nadal mogą uniemożliwiać oferowanie protokołów TLS 1,2. Może to spowodować błędy, w tym między innymi następujące:

- Operacje tworzenia kopii zapasowej i przywracania mogą zakończyć się niepowodzeniem.
- Błędy połączeń składników kopii zapasowej z błędem 10054 (istniejące połączenie zostało wymuszone przez hosta zdalnego).
- Usługi związane z Azure Backup nie będą zatrzymywane ani uruchamiane w zwykły sposób.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Protokół Transport Layer Security](https://docs.microsoft.com/windows/win32/secauthn/transport-layer-security-protocol)
- [Zapewnianie obsługi protokołu TLS 1,2 w ramach wdrożonych systemów operacyjnych](https://docs.microsoft.com/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [Najlepsze rozwiązania w zakresie protokołu TLS (Transport Layer Security) z .NET Framework](https://docs.microsoft.com/dotnet/framework/network-programming/tls)
