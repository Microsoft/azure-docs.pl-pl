---
title: 'Azure VPN Gateway: Konfigurowanie przechwytywania pakietów'
description: Dowiedz się więcej na temat funkcji przechwytywania pakietów, których można używać na bramach sieci VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 3ba3046367ceece6bf0ddf157451025c79977324
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077202"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Konfigurowanie przechwytywania pakietów dla bram sieci VPN

Problemy związane z łącznością i wydajnością są często skomplikowane i bardzo dużo czasu i wysiłku wystarczy, aby zawęzić przyczynę problemu. Możliwość przechwycenia pakietów znacznie pomaga skrócić czas ograniczania zakresu problemu do określonych części sieci, na przykład to, czy problem znajduje się po stronie klienta sieci, stronie platformy Azure, czy w innym miejscu. Po zawężaniu problemu jest znacznie bardziej wydajne debugowanie i podejmowanie akcji zaradczych.

Dostępne są kilka powszechnie dostępnych narzędzi do przechwytywania pakietów. Pobieranie odpowiednich przechwycenia pakietu za pomocą tych narzędzi może być uciążliwe, szczególnie podczas pracy z dużą ilością scenariuszy ruchu sieciowego. Możliwości filtrowania udostępniane przez funkcję przechwytywania pakietów bramy sieci VPN stają się głównym rozróżnieniem. Oprócz powszechnie dostępnych narzędzi do przechwytywania pakietów można użyć funkcji przechwytywania pakietów bramy sieci VPN.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Możliwości filtrowania przechwytywania pakietów przez bramę sieci VPN

Przechwytywanie pakietów usługi VPN Gateway można uruchamiać na bramie lub w określonym połączeniu w zależności od potrzeb klientów. Możesz również uruchomić przechwycenia pakietów na wielu tunelach w tym samym czasie. Można przechwytywać ruch pojedynczy lub dwukierunkowy, ruch IKE i ESP oraz wewnętrzne pakiety wraz z filtrowaniem na bramie sieci VPN.

Używanie filtru z pięcioma kolekcjami (podsieć źródłowa, podsieć docelowa, port źródłowy, port docelowy, protokół) i flagi TCP (SYN, ACK, FIN, URG, PSH —, RST) jest przydatne w przypadku izolowania problemów dotyczących dużej ilości ruchu sieciowego.

Zapoznaj się z poniższym przykładem JSON i schemat JSON z objaśnieniem każdej właściwości. Należy również pamiętać o pewnych ograniczeniach podczas uruchamiania przechwytywania pakietów:
- W schemacie filtr jest pokazywany jako tablica, ale w danym momencie może być używany tylko jeden filtr.
- Nie można jednocześnie przechwycić wielu pakietów bramy w tym samym czasie.
- Wielokrotne przechwycenia pakietu na tym samym połączeniu nie są dozwolone. Przechwycenia pakietu można uruchomić na różnych połączeniach w tym samym czasie.
- Maksymalnie pięć przechwyconych pakietów można uruchamiać równolegle na bramę. Te przechwycenia pakietu mogą być kombinacją przechwytywania pakietów dla całej bramy lub przechwycenia pakietu dla połączenia.

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

## <a name="setup-packet-capture-using-powershell"></a>Konfigurowanie przechwytywania pakietów przy użyciu programu PowerShell

Zapoznaj się z poniższymi przykładami poleceń programu PowerShell, aby uruchomić i zatrzymać przechwytywanie pakietów. Aby uzyskać więcej informacji na temat opcji parametrów, zobacz ten [dokument](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)programu PowerShell.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Rozpocznij przechwytywanie pakietów dla bramy sieci VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Opcjonalny parametr **-danych filtru** może służyć do zastosowania filtru.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Zatrzymywanie przechwytywania pakietu dla bramy sieci VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Rozpocznij przechwytywanie pakietów dla połączenia bramy sieci VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Opcjonalny parametr **-danych filtru** może służyć do zastosowania filtru.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Zatrzymywanie przechwytywania pakietu na połączeniu z bramą sieci VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Najważniejsze zagadnienia

- Przechwytywanie pakietów może mieć wpływ na wydajność. Pamiętaj, aby zatrzymać przechwytywanie pakietów, gdy nie jest to konieczne.
- Sugerowany minimalny czas trwania przechwytywania pakietów to 600 sekund. Krótszy czas trwania przechwytywania pakietów może nie zapewniać kompletnych danych ze względu na zsynchronizowanie problemów między wieloma składnikami ścieżki.
- Pliki danych przechwytywania pakietów są generowane w formacie PCAP. Otwórz pliki PCAP za pomocą programu Wireshark lub innych powszechnie dostępnych aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat VPN Gateway, zobacz [Informacje o VPN Gateway](vpn-gateway-about-vpngateways.md)
