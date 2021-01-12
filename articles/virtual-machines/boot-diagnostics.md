---
title: Diagnostyka rozruchu platformy Azure
description: Omówienie diagnostyki rozruchu platformy Azure i zarządzanej diagnostyki rozruchu
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 3ae300ca2746ab9e3478d3fe14fd6fc49c95a93d
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071735"
---
# <a name="azure-boot-diagnostics"></a>Diagnostyka rozruchu platformy Azure

Diagnostyka rozruchu to funkcja debugowania dla maszyn wirtualnych platformy Azure, która umożliwia Diagnozowanie błędów rozruchu maszyn wirtualnych. Diagnostyka rozruchu umożliwia użytkownikowi obserwowanie stanu swojej maszyny wirtualnej w trakcie jej uruchamiania przez zbieranie informacji o dzienniku seryjnym i zrzuty ekranu.

## <a name="boot-diagnostics-storage-account"></a>Konto magazynu diagnostyki rozruchu
Podczas tworzenia maszyny wirtualnej w Azure Portal Diagnostyka rozruchu jest domyślnie włączona. Zalecanym działaniem diagnostyki rozruchu jest użycie zarządzanego konta magazynu, ponieważ zapewnia ono znaczną poprawę wydajności w czasie tworzenia maszyny wirtualnej platformy Azure. Jest to spowodowane tym, że używane jest konto magazynu zarządzanego przez platformę Azure, usuwając czas potrzebny do utworzenia nowego konta magazynu użytkownika w celu przechowywania danych diagnostycznych rozruchowych.

> [!IMPORTANT]
> Obiekty BLOB danych diagnostyki rozruchu (które składają się z dzienników i obrazów migawek) są przechowywane na zarządzanym koncie magazynu. Klienci będą obciążani wyłącznie GiBs używanymi przez obiekty blob, a nie na dysku o nieobsługiwanym rozmiarze. Liczniki migawek zostaną użyte do rozliczania zarządzanego konta magazynu. Ze względu na to, że konta zarządzane są tworzone przy użyciu standardowej usługi LRS lub standardowej ZRS, w celu uzyskania rozmiaru obiektów BLOB danych diagnostycznych klienci będą obciążani za $0,05/GB miesięcznie. Aby uzyskać więcej informacji na temat tych cen, zobacz [Cennik usługi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). Klienci będą widzieć tę opłatę powiązana z identyfikatorem URI zasobu maszyny wirtualnej. 

Alternatywna Obsługa diagnostyki rozruchu polega na użyciu konta magazynu zarządzanego przez użytkownika. Użytkownik może utworzyć nowe konto magazynu lub użyć istniejącego.
> [!NOTE]
> Konta magazynu zarządzane przez użytkownika skojarzone z diagnostyką rozruchu wymagają konta magazynu i skojarzonych maszyn wirtualnych znajdujących się w tej samej subskrypcji. 



## <a name="boot-diagnostics-view"></a>Widok diagnostyki rozruchu
W bloku maszyna wirtualna opcja Diagnostyka rozruchu znajduje się w sekcji *Pomoc techniczna i rozwiązywanie problemów* w Azure Portal. Wybranie opcji Diagnostyka rozruchu spowoduje wyświetlenie zrzutu ekranu i informacji o dzienniku seryjnym. Dziennik seryjny zawiera komunikaty jądra, a zrzut ekranu jest migawką bieżącego stanu maszyn wirtualnych. Na podstawie tego, czy na maszynie wirtualnej jest uruchomiony system Windows lub Linux, decyduje o tym, jak będzie wyglądać oczekiwany zrzut ekranu. W przypadku systemu Windows użytkownicy zobaczą tło pulpitu i system Linux zobaczy monit logowania.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Zrzut ekranu diagnostyki rozruchu systemu Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Zrzut ekranu przedstawiający diagnostykę rozruchu systemu Windows":::

## <a name="enable-managed-boot-diagnostics"></a>Włącz diagnostykę zarządzanego rozruchu 
Zarządzaną diagnostykę rozruchu można włączyć za pomocą szablonów Azure Portal, CLI i ARM. Włączanie przy użyciu programu PowerShell nie jest jeszcze obsługiwane. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Włączanie zarządzanej diagnostyki rozruchu przy użyciu Azure Portal
W przypadku tworzenia maszyny wirtualnej w Azure Portal domyślnym ustawieniem jest włączenie diagnostyki rozruchu przy użyciu zarządzanego konta magazynu. Aby je wyświetlić, przejdź do karty *Zarządzanie* podczas tworzenia maszyny wirtualnej. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="Zrzut ekranu włączania zarządzanej diagnostyki rozruchu podczas tworzenia maszyny wirtualnej.":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>Włączanie zarządzanej diagnostyki rozruchu przy użyciu interfejsu wiersza polecenia
Diagnostyka rozruchu z zarządzanym kontem magazynu jest obsługiwana w interfejsie wiersza polecenia platformy Azure 2.12.0 i nowszych. Jeśli nie wpiszesz nazwy lub identyfikatora URI dla konta magazynu, zostanie użyte konto zarządzane. Aby uzyskać więcej informacji i przykładów kodu, zobacz [dokumentację interfejsu wiersza polecenia dla diagnostyki rozruchu](/cli/azure/vm/boot-diagnostics?preserve-view=true&view=azure-cli-latest).

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>Włączanie zarządzanej diagnostyki rozruchu przy użyciu szablonów Azure Resource Manager (ARM)
Wszystko po interfejsie API w wersji 2020-06-01 obsługuje zarządzaną diagnostykę rozruchu. Aby uzyskać więcej informacji, zobacz [Widok wystąpienia diagnostyki rozruchu](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics).

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>Ograniczenia
- Diagnostyka rozruchu jest dostępna tylko dla maszyn wirtualnych Azure Resource Manager.
- Diagnostyka zarządzanego rozruchu nie obsługuje maszyn wirtualnych korzystających z niezarządzanych dysków systemu operacyjnego.
- Diagnostyka rozruchu nie obsługuje kont usługi Premium Storage, jeśli konto magazynu w warstwie Premium jest używane na potrzeby diagnostyki rozruchu, `StorageAccountTypeNotSupported` podczas uruchamiania maszyny wirtualnej wystąpi błąd. 
- Zarządzane konta magazynu są obsługiwane w Menedżer zasobów interfejsie API w wersji "2020-06-01" i nowszych.
- Usługa Azure serial Console jest obecnie niezgodna z zarządzanym kontem magazynu na potrzeby diagnostyki rozruchu. Dowiedz się więcej o [usłudze Azure serial Console](./troubleshooting/serial-console-overview.md).
- Portal obsługuje tylko używanie diagnostyki rozruchu z zarządzanym kontem magazynu dla maszyn wirtualnych z pojedynczym wystąpieniem.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [konsoli szeregowej platformy Azure](./troubleshooting/serial-console-overview.md) i sposobach [rozwiązywania problemów z maszynami wirtualnymi na platformie Azure](./troubleshooting/boot-diagnostics.md)przy użyciu diagnostyki rozruchu.
