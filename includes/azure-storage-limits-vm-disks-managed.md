---
title: включение файла
description: включение файла
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dcd94cbd7963c13bd739b1b004fe5ac506d3ea9d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006519"
---
**Управляемые диски виртуальной машины (цен. категория "Стандартный")**

| Диски уровня "Стандартный"  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60              | S70              | S80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Размер диска (ГиБ)          | 32             | 64             | 128            | 256  | 512            | 1024    | 2048     | 4095    | 8192     | 16 384     | 32 767     |
| Количество операций ввода-вывода в секунду на диск       | 500              | 500              | 500              | 500 | 500              | 500              | 500             | 500              | До 1300              | До 2000              | До 2000              |
| Пропускная способность на диск | До 60 МиБ/с | До 60 МиБ/с | До 60 МиБ/с | До 60 МиБ/с | До 60 МиБ/с | До 60 МиБ/с | До 60 МиБ/с | До 60 МиБ/с| До 300 МиБ/с | До 500 МиБ/с | До 500 МиБ/с |

**Диски SSD виртуальной машины (цен. категория "Стандартный")**

| Тип диска SSD уровня "Стандартный"  | E10               | E15               | E20             | E30 | E40              | E50              | E60              | E70              | E80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Размер диска (ГиБ)           | 128             | 256             | 512            | 1024  | 2048            | 4095     | 8192     | 16 384     | 32 767    |
| Количество операций ввода-вывода в секунду на диск       | 500              | 500              | 500              | 500 | 500              | 500              | 500             | 500              | До 1300              | До 2000              | До 2000              |
| Пропускная способность на диск | До 60 МБ/с | До 60 МБ/с | До 60 МБ/с | До 60 МБ/с | До 60 МБ/с | До 60 МБ/с | До 60 МБ/с | До 60 МБ/с| До 300 МиБ/с |  До 500 МиБ/с | До 500 МиБ/с |

**Управляемые диски виртуальной машины уровня "Премиум": ограничения на диск**

| Диск (цен. категория "Премиум")  | P4               | P6               | P10             | P15 | P20              | S30              | P40              | P50              | P60              | P70              | P80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Размер диска (ГиБ)           | 32             | 64             | 128            | 256  | 512            | 1024    | 2048     | 4095    | 8192     | 16 384     | 32 767     |
| Количество операций ввода-вывода в секунду на диск       | До 120 | До 240              | 500              | До 1100 | До 2300              | До 5000              | До 7500             | До 7500              | До 12 500              | До 15 000              | До 20 000              |
| Пропускная способность на диск | До 25 МиБ/с | До 50 МиБ/с | До 100 МиБ/с | До 125 МиБ/с | До 150 МиБ/с | До 200 МиБ/с | До 250 МиБ/с | До 250 МиБ/с| До 480 МиБ/с | До 750 МиБ/с | До 750 МиБ/с |

**Управляемые диски виртуальной машины уровня "Премиум": ограничения на виртуальную машину**

| Ресурс | Ограничение по умолчанию |
| --- | --- |
| Максимальное количество операций ввода-вывода в секунду на виртуальную машину |80 000 операций ввода-вывода в секунду для виртуальной машины GS5 |
| Максимальная пропускная способность на виртуальную машину |2000 МБ/с для виртуальной машины GS5 |
