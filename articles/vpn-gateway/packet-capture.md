---
title: 'VPN Gateway platformy Azure: Konfigurowanie przechwytywania pakietów'
description: Informacje na temat funkcji przechwytywania pakietów, których można użyć na bramach sieci VPN, aby pomóc w zawężaniu przyczyny problemu.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: radwiv
ms.openlocfilehash: 3be01f6d8e1fb1f6ba541f8d1cb0c92d2a43b0da
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073108"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>Konfigurowanie przechwytywania pakietów dla bram sieci VPN

Problemy związane z łącznością i wydajnością są często skomplikowane. Zawężanie przyczyny problemu może zająć dużo czasu i wysiłku. Przechwytywanie pakietów może pomóc w zawężaniu zakresu problemu do określonych części sieci. Może pomóc w ustaleniu, czy problem znajduje się po stronie klienta sieci, po stronie platformy Azure lub w innym miejscu. Po zawężaniu problemu bardziej wydajne jest debugowanie i podejmowanie akcji zaradczych.

Dostępne są popularne narzędzia do przechwytywania pakietów. Pobieranie odpowiednich przechwycenia pakietu za pomocą tych narzędzi może być uciążliwe, szczególnie w scenariuszach ruchu o dużej pojemności. Możliwości filtrowania oferowane przez funkcję przechwytywania pakietów VPN Gateway platformy Azure są głównym rozróżnieniem. Możesz użyć przechwycenia pakietu VPN Gateway razem z powszechnie dostępnymi narzędziami do przechwytywania pakietów.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Możliwości filtrowania przechwytywania pakietów VPN Gateway

W zależności od potrzeb można uruchomić funkcję przechwytywania pakietów VPN Gateway na bramie lub w konkretnym połączeniu. Możesz również uruchomić funkcję przechwytywania pakietów na wielu tunelach jednocześnie. Można przechwytywać ruch jednokierunkowy lub dwukierunkowy, ruch IKE i ESP oraz wewnętrzne pakiety wraz z filtrowaniem na bramie sieci VPN.

Warto użyć filtru z pięcioma krotkami (podsieć źródłowa, podsieć docelowa, port źródłowy, port docelowy, protokół) i flagi TCP (SYN, ACK, FIN, URG, PSH —, RST) w przypadku izolowania problemów w ruchu dużej ilości danych.

Poniższe przykłady danych JSON i schematu JSON zawierają wyjaśnienia każdej właściwości. Poniżej przedstawiono niektóre ograniczenia, które należy wziąć pod uwagę podczas uruchamiania przechwytywania pakietów:
- W schemacie widocznym tutaj filtr jest tablicą, ale w danym momencie może być używany tylko jeden filtr.
- W tym samym czasie nie można uruchomić wielu przechwyconych pakietów bramy.
- Nie można uruchomić wielu przechwycenia pakietu jednocześnie dla jednego połączenia. W tym samym czasie można uruchomić wiele przechwyconych pakietów na różnych połączeniach.
- Maksymalnie pięć przechwyconych pakietów można uruchamiać równolegle na bramę. Te przechwycenia pakietu mogą być kombinacją przechwyconych pakietów bramy i przechwytywania pakietów dla poszczególnych połączeń.

### <a name="example-json"></a>Przykładowy kod JSON
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>Schemat JSON
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="set-up-packet-capture-by-using-powershell"></a>Konfigurowanie przechwytywania pakietów przy użyciu programu PowerShell

W poniższych przykładach przedstawiono polecenia programu PowerShell, które uruchamiają i zatrzymują przechwytywanie pakietów. Aby uzyskać więcej informacji na temat opcji parametrów, zobacz [Start-AzVirtualnetworkGatewayPacketCapture](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Rozpocznij przechwytywanie pakietów dla bramy sieci VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Aby zastosować filtr, można użyć opcjonalnego parametru `-FilterData` .

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Zatrzymywanie przechwytywania pakietu dla bramy sieci VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Rozpocznij przechwytywanie pakietów dla połączenia bramy sieci VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Aby zastosować filtr, można użyć opcjonalnego parametru `-FilterData` .

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Zatrzymywanie przechwytywania pakietu na połączeniu z bramą sieci VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Najważniejsze zagadnienia

- Przechwycenie pakietu może mieć wpływ na wydajność. Pamiętaj, aby zatrzymać przechwytywanie pakietów, gdy nie jest to potrzebne.
- Sugerowany minimalny czas trwania przechwytywania pakietów to 600 sekund. Ze względu na problemy z synchronizacją między wieloma składnikami w ścieżce krótsze przechwycenia pakietów może nie zapewniać kompletnych danych.
- Pliki danych przechwytywania pakietów są generowane w formacie PCAP. Otwórz pliki PCAP za pomocą programu Wireshark lub innych powszechnie dostępnych aplikacji.
- Przechwytywanie pakietów nie jest obsługiwane w bramach opartych na zasadach.
- Jeśli `SASurl` parametr nie jest prawidłowo skonfigurowany, śledzenie może zakończyć się niepowodzeniem z błędami magazynu. Aby zapoznać się z przykładami prawidłowego generowania `SASurl` parametru, zobacz [stop-AzVirtualNetworkGatewayPacketCapture](https://docs.microsoft.com/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture).



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat VPN Gateway, zobacz [co to jest VPN Gateway?](vpn-gateway-about-vpngateways.md).
