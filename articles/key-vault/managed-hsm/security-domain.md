---
title: Informacje o domenie zabezpieczeń zarządzanego modułu HSM platformy Azure
description: Omówienie zarządzanej domeny zabezpieczeń modułu HSM, zestaw podstawowych poświadczeń wymaganych do odzyskania zarządzanego modułu HSM
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 09/15/2020
ms.openlocfilehash: 37e2541d0b53c96fd3f85da31b2c0ce5b68b551a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90997213"
---
# <a name="about-the-managed-hsm-security-domain"></a>Informacje o domenie zabezpieczeń zarządzanego modułu HSM

Zarządzana domena zabezpieczeń modułu HSM (SD) to zestaw podstawowych poświadczeń, które są potrzebne do odzyskania zarządzanego modułu HSM w przypadku wystąpienia awarii. Domena zabezpieczeń jest generowana w zarządzanym sprzęcie modułu HSM oraz enclaves oprogramowania usługi i reprezentuje "własność" modułu HSM.

Każdy zarządzany moduł HSM musi mieć domenę zabezpieczeń do działania. Po zażądaniu nowego zarządzanego modułu HSM jest on inicjowany, ale nie jest aktywowany do momentu pobrania domeny zabezpieczeń. Gdy zarządzany moduł HSM jest zainicjowany, ale nie jest aktywowany, stan, istnieją dwa sposoby jego aktywowania:
- Pobranie domeny zabezpieczeń jest metodą domyślną i pozwala bezpiecznie przechowywać domenę zabezpieczeń do użycia z innym zarządzanym modułem HSM lub odzyskać sprawność po całkowitej awarii.
- Przekaż już istniejącą domenę zabezpieczeń, która umożliwia utworzenie wielu zarządzanych wystąpień modułu HSM, które współużytkują tę samą domenę zabezpieczeń.

## <a name="download-your-security-domain"></a>Pobierz domenę zabezpieczeń

Gdy zarządzany moduł HSM jest inicjowany, ale nie jest aktywowany, pobranie domeny zabezpieczeń przechwytuje podstawowe poświadczenia potrzebne do odzyskania po pełnej utracie całego sprzętu. Aby pobrać domenę zabezpieczeń, należy utworzyć co najmniej 3 (maksymalnie 10) par kluczy RSA i wysłać te klucze publiczne do usługi podczas żądania pobrania domeny zabezpieczeń. Należy również określić minimalną wymaganą liczbę kluczy (kworum), aby odszyfrować domenę zabezpieczeń w przyszłości. Zarządzany moduł HSM zainicjuje domenę zabezpieczeń i zaszyfruje ją przy użyciu kluczy publicznych, które są udostępniane za pomocą [algorytmu udostępniania klucza tajnego Shamir](https://dl.acm.org/doi/10.1145/359168.359176). Pobrany obiekt BLOB domeny zabezpieczeń można odszyfrować tylko wtedy, gdy są dostępne co najmniej kworum kluczy prywatnych; należy zachować bezpieczeństwo kluczy prywatnych, aby zapewnić bezpieczeństwo domeny zabezpieczeń. Po zakończeniu pobierania zarządzany moduł HSM będzie gotowy do użycia.  

> [!IMPORTANT]
> W przypadku pełnego odzyskiwania po awarii należy mieć domenę zabezpieczeń i kworum kluczy prywatnych, które były używane do ochrony, oraz pełną kopię zapasową modułu HSM. Jeśli do utraty kworum zostanie utracona domena zabezpieczeń lub wystarczające są klucze RSA (klucz prywatny), a żadne uruchomione wystąpienia zarządzanego modułu HSM nie są dostępne, odzyskiwanie po awarii nie będzie możliwe.

## <a name="upload-a-security-domain"></a>Przekaż domenę zabezpieczeń

Gdy zarządzany moduł HSM jest inicjowany, ale nie jest aktywowany, można zainicjować proces odzyskiwania domeny zabezpieczeń. Zarządzany moduł HSM wygeneruje parę kluczy RSA i zwróci klucz publiczny. Następnie można przekazać domenę zabezpieczeń do zarządzanego modułu HSM. Przed przekazaniem klient (interfejs wiersza polecenia platformy Azure lub program PowerShell) musi zostać dostarczony z minimalnym numerem kworum kluczy prywatnych użytych podczas pobierania domeny zabezpieczeń. Klient odszyfruje domenę zabezpieczeń przy użyciu tego kworum i zaszyfruje go przy użyciu klucza publicznego pobranego podczas zażądanego odzyskiwania. Po zakończeniu przekazywania zarządzany moduł HSM będzie w stanie aktywowany.

## <a name="backup-and-restore-behavior"></a>Zachowanie tworzenia kopii zapasowych i przywracania

Kopie zapasowe (pełna kopia zapasowa lub jednorazowa kopia zapasowa klucza) można przywrócić pomyślnie tylko wtedy, gdy zarządzany jest źródłowy moduł HSM (w którym utworzono kopię zapasową) i docelowy zarządzany moduł HSM (do którego kopia zapasowa zostanie przywrócona). W ten sposób domena zabezpieczeń definiuje także granicę kryptograficzną dla każdego zarządzanego modułu HSM.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z omówieniem zarządzanego modułu HSM](overview.md)
- Informacje o [zarządzaniu zarządzanym modułem HSM przy użyciu interfejsu wiersza polecenia platformy Azure](key-management.md)
- Przegląd [najlepszych](best-practices.md) rozwiązań modułu HSM
