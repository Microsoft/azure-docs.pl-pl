---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: b0be074c995fcc62f63a4a2ebf4149d3040e7b6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750175"
---
## <a name="best-practices"></a>Najlepsze rozwiązania

W tej sekcji przedstawiono niektóre najlepsze rozwiązania dotyczące ustawiania list ACL rekursywnie. 

#### <a name="handling-runtime-errors"></a>Obsługa błędów środowiska uruchomieniowego

Błąd w czasie wykonywania może być spowodowany wieloma przyczynami (na przykład: awaria lub problem z łącznością klienta). Jeśli wystąpi błąd środowiska uruchomieniowego, należy ponownie uruchomić proces cyklicznej listy ACL. Listy ACL można ponownie zastosować do elementów bez powodowania negatywnego wpływu. 

#### <a name="handling-permission-errors-403"></a>Obsługa błędów uprawnień (403)

Jeśli wystąpi wyjątek kontroli dostępu podczas wykonywania cyklicznego procesu listy ACL, [podmiot zabezpieczeń](../articles/role-based-access-control/overview.md#security-principal) usługi AD może nie mieć wystarczających uprawnień, aby zastosować listę ACL do co najmniej jednego elementu podrzędnego w hierarchii katalogów. W przypadku wystąpienia błędu uprawnień proces zostaje zatrzymany, a podano token kontynuacji. Usuń problem z uprawnieniami, a następnie użyj tokenu kontynuacji, aby przetworzyć pozostały zestaw danych. Katalogi i pliki, które zostały już pomyślnie przetworzone, nie będą musiały zostać przetworzone ponownie. Możesz również wybrać opcję ponownego uruchomienia cyklicznego procesu listy ACL. Listy ACL można ponownie zastosować do elementów bez powodowania negatywnego wpływu. 

#### <a name="credentials"></a>Poświadczenia 

Zalecamy zainicjowanie podmiotu zabezpieczeń usługi Azure AD, który ma przypisaną rolę [właściciela danych obiektów blob magazynu](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) w zakresie docelowego konta magazynu lub kontenera. 

#### <a name="performance"></a>Wydajność 

Aby zmniejszyć opóźnienie, zalecamy uruchomienie cyklicznego procesu listy ACL na maszynie wirtualnej platformy Azure, która znajduje się w tym samym regionie co konto magazynu. 

#### <a name="acl-limits"></a>Limity listy ACL

Maksymalna liczba list ACL, które można zastosować do katalogu lub pliku, to 32 listy ACL dostępu i domyślne listy ACL 32. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).