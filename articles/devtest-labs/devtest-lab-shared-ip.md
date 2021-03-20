---
title: Informacje o udostępnionych adresach IP w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, w jaki sposób Azure DevTest Labs używać udostępnionych adresów IP, aby zminimalizować publiczne adresy IP wymagane do uzyskania dostępu do maszyn wirtualnych laboratorium.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 36a5da1b2b6252d0adb480a622c461b33425e675
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85484098"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Informacje o udostępnionych adresach IP w Azure DevTest Labs

Azure DevTest Labs umożliwia maszynom wirtualnym laboratorium współużytkowanie tego samego publicznego adresu IP w celu zminimalizowania liczby publicznych adresów IP wymaganych do uzyskania dostępu do poszczególnych maszyn wirtualnych laboratorium.  W tym artykule opisano sposób działania współużytkowanych adresów IP i związanych z nimi opcji konfiguracji.

## <a name="shared-ip-setting"></a>Ustawienie udostępnionego adresu IP

Tworzenie laboratorium jest tworzone w podsieci sieci wirtualnej.  Domyślnie ta podsieć jest tworzona z opcją **Włącz współużytkowany publiczny adres IP** ustawiony na *wartość tak*.  Ta konfiguracja tworzy jeden publiczny adres IP dla całej podsieci.  Aby uzyskać więcej informacji na temat konfigurowania sieci wirtualnych i podsieci, zobacz [Konfigurowanie sieci wirtualnej w Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nowa podsieć laboratorium](media/devtest-lab-shared-ip/lab-subnet.png)

W przypadku istniejących laboratoriów tę opcję można włączyć, wybierając kolejno pozycje **Konfiguracja i zasady > sieci wirtualne**. Następnie wybierz sieć wirtualną z listy i wybierz opcję **Włącz WSPÓŁUŻYTKOWANY publiczny adres IP** dla wybranej podsieci. Możesz również wyłączyć tę opcję w dowolnym laboratorium, jeśli nie chcesz udostępniać publicznego adresu IP między maszynami wirtualnymi laboratorium.

Wszystkie maszyny wirtualne utworzone w tym laboratorium domyślnie korzystają ze współużytkowanego adresu IP.  Podczas tworzenia maszyny wirtualnej to ustawienie można zaobserwować na stronie **Ustawienia zaawansowane** w obszarze **Konfiguracja adresu IP**.

![Nowa maszyna wirtualna](media/devtest-lab-shared-ip/new-vm.png)

- **Udostępnione:** Wszystkie maszyny wirtualne utworzone jako **udostępnione** są umieszczane w jednej grupie zasobów (RG). Do tego RG zostanie przypisany pojedynczy adres IP, a wszystkie maszyny wirtualne w RG będą używać tego adresu IP.
- **Publiczny:** Każda utworzona maszyna wirtualna ma swój własny adres IP i jest tworzona w jego własnej grupie zasobów.
- **Prywatny:** Każda utworzona maszyna wirtualna używa prywatnego adresu IP. Nie można połączyć się z tą maszyną wirtualną bezpośrednio z Internetu za pomocą Pulpit zdalny.

Po dodaniu maszyny wirtualnej z włączonym udostępnionym adresem IP do podsieci, DevTest Labs automatycznie dodaje maszynę wirtualną do modułu równoważenia obciążenia i przypisze numer portu TCP na publicznym adresie IP, przekazując do portu RDP na maszynie wirtualnej.  

## <a name="using-the-shared-ip"></a>Korzystanie ze współużytkowanego adresu IP

- **Użytkownicy systemu Linux:** SSH z maszyną wirtualną przy użyciu adresu IP lub w pełni kwalifikowanej nazwy domeny, po którym następuje port. Na przykład na poniższym obrazie adres RDP, aby połączyć się z maszyną wirtualną `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661` .

  ![Przykład maszyny wirtualnej](media/devtest-lab-shared-ip/vm-info.png)

- **Użytkownicy systemu Windows:** Wybierz przycisk **Połącz** na Azure Portal, aby pobrać wstępnie SKONFIGUROWANY plik RDP i uzyskać dostęp do maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

* [Definiowanie zasad laboratorium w Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Konfigurowanie sieci wirtualnej w Azure DevTest Labs](devtest-lab-configure-vnet.md)





