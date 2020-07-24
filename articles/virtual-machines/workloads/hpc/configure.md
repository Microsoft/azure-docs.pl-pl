---
title: Obliczenia o wysokiej wydajności — Virtual Machines platformy Azure | Microsoft Docs
description: Dowiedz się więcej o wysokiej wydajności obliczeniowej na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 723419b97dc024a700d860dd3fe61ff48073a587
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020002"
---
# <a name="optimization-for-linux"></a>Optymalizacja na potrzeby systemu Linux

W tym artykule przedstawiono kilka kluczowych technik optymalizacji obrazu systemu operacyjnego. Dowiedz się więcej na temat [włączania funkcji InfiniBand](enable-infiniband.md) i optymalizowania obrazów systemu operacyjnego.

## <a name="update-lis"></a>Aktualizacja LIS

W przypadku wdrażania przy użyciu obrazu niestandardowego (na przykład starszego systemu operacyjnego, takiego jak CentOS/RHEL 7,4 lub 7,5), należy zaktualizować funkcję LIS na maszynie wirtualnej.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Odzyskiwanie pamięci

Zwiększenie wydajności przez automatyczne odzyskiwanie pamięci w celu uniknięcia dostępu do pamięci zdalnej.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Aby to zrobić, po ponownym uruchomieniu maszyny wirtualnej:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Wyłącz zaporę i SELinux

Wyłącz zaporę i SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Wyłącz cpupower

Wyłącz cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [włączania funkcji InfiniBand](enable-infiniband.md) i optymalizowania obrazów systemu operacyjnego.

* Dowiedz się więcej o [HPC](/azure/architecture/topics/high-performance-computing/) na platformie Azure.
