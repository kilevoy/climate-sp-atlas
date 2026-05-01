# Описание структуры settlements-climate.json

Файл — массив JSON-объектов, по одному на населённый пункт (всего 205). Каждый объект имеет следующие поля:

## Базовые поля

| Поле | Тип | Описание |
|---|---|---|
| `id` | string | Машинный идентификатор (snake_case): `"moscow"`, `"saint_petersburg"`. |
| `country` | string | Страна. Сейчас всегда `"Россия"`. |
| `region` | string | Субъект РФ. |
| `settlement` | string | Название на русском. |
| `settlementType` | string | `"город"` / `"пгт"` / `"село"` / `"деревня"`. |

## snow — снеговая нагрузка (СП 20.13330.2016, карта 1)

```json
"snow": {
  "region": "III",
  "sgKpa": 1.5,
  "source": "...",
  "status": "verified"
}
```

| Поле | Тип | Описание |
|---|---|---|
| `region` | римская цифра | Снеговой район `I…VIII`. |
| `sgKpa` | number \| null | Нормативное значение веса снегового покрова Sg, кПа. |
| `source` | string | Источник: ссылка на СП и/или калькулятор. |
| `status` | enum | `verified` / `from_sp20_appendix_k` / `requires_verification` / `manual` / `not_applicable`. |

## wind — ветровая нагрузка (СП 20.13330.2016, карта 3)

```json
"wind": { "region": "I", "w0Kpa": 0.23, "source": "...", "status": "verified" }
```

| Поле | Тип | Описание |
|---|---|---|
| `region` | строка | Ветровой район `Ia, I…VII`. |
| `w0Kpa` | number \| null | Нормативное значение w0, кПа. |
| `source`, `status` | — | См. выше. |

## ice — гололёдная нагрузка (СП 20.13330.2016, карта 4)

```json
"ice": { "region": "II", "iceThicknessMm": 5.0, "source": "...", "status": "verified" }
```

| Поле | Тип | Описание |
|---|---|---|
| `region` | римская цифра | Гололёдный район `I…V`. |
| `iceThicknessMm` | number \| null | Толщина стенки гололёда b на h = 10 м, мм. |
| `source`, `status` | — | См. выше. |

## seismic — сейсмика (СП 14.13330, ОСР-2015)

```json
"seismic": { "points": 5, "source": "...", "status": "verified" }
```

| Поле | Тип | Описание |
|---|---|---|
| `points` | integer \| null | Сейсмичность в баллах по карте A ОСР-2015 (массовое строительство). Карты B и C — в outputs/ese-data.json. |
| `source`, `status` | — | См. выше. |

## terrain — тип местности (СП 20.13330.2016, табл. 11.2)

```json
"terrain": {
  "defaultType": null,
  "allowedTypes": ["A", "B", "C"]
}
```

| Поле | Тип | Описание |
|---|---|---|
| `defaultType` | enum \| null | По умолчанию `null` — задаёт инженер при расчёте. |
| `allowedTypes` | array | Допустимые типы: обычно все три `["A", "B", "C"]`. |

## Флаги управления

| Поле | Тип | Описание |
|---|---|---|
| `manualAllowed` | bool | Можно ли инженеру переопределить значения вручную. |
| `expertOverrideAllowed` | bool | Можно ли при экспертизе переопределить нагрузки. |
| `dataStatus` | enum | Общий статус записи: `verified` / `partial` / `requires_verification`. |
| `comment` | string | Свободный комментарий: контрольный город, уточнённые значения, особенности. |

---

## Расширенная схема (опциональные поля)

Эти поля могут отсутствовать у некоторых городов, если ese.pro их не показал (для 78 из 205 нет ближайшей метеостанции по СП 131).

### frostDepth — глубина сезонного промерзания (СП 22.13330, м)

```json
"frostDepth": {
  "loamy": 1.03,
  "sandyFine": 1.25,
  "sandyCoarse": 1.34,
  "coarseFragmental": 1.52,
  "source": "...",
  "status": "verified"
}
```

| Поле | Тип | Описание |
|---|---|---|
| `loamy` | number \| null | Суглинки и глины, м. |
| `sandyFine` | number \| null | Супеси, пески мелкие и пылеватые, м. |
| `sandyCoarse` | number \| null | Пески крупные и средней крупности, м. |
| `coarseFragmental` | number \| null | Крупнообломочные грунты, м. |

### coldPeriod — холодный период (СП 131.13330)

```json
"coldPeriod": {
  "tempColdestDay098": -31.0,
  "tempColdestDay092": -28.0,
  ...
}
```

| Поле | Тип | Описание |
|---|---|---|
| `tempColdestDay098` | number \| null | T наиболее холодных суток с обеспеч. 0.98, °C. |
| `tempColdestDay092` | number \| null | То же, обеспеч. 0.92. |
| `tempColdest5days098` | number \| null | T наиболее холодной пятидневки, обеспеч. 0.98. |
| `tempColdest5days092` | number \| null | То же, обеспеч. 0.92. |
| `temp094` | number \| null | T с обеспеч. 0.94. |
| `absMin` | number \| null | Абсолютная минимальная T. |
| `dailyAmplitude` | number \| null | Средняя суточная амплитуда T наиболее холодного месяца. |
| `durationLe0` | integer \| null | Продолжительность периода с T ≤ 0 °C, сут. |
| `meanLe0` | number \| null | Средняя T этого периода. |
| `durationLe8` | integer \| null | Продолжительность отопительного периода (T ≤ 8 °C), сут. |
| `meanLe8` | number \| null | Средняя T отопительного периода. |
| `durationLe10` | integer \| null | Продолжительность периода с T ≤ 10 °C, сут. |
| `meanLe10` | number \| null | Средняя T этого периода. |
| `humidityCold` | integer \| null | Относительная влажность наиболее холодного месяца, %. |
| `humidityCold15` | integer \| null | То же в 15:00, %. |
| `precipNovMar` | integer \| null | Сумма осадков ноябрь—март, мм. |
| `prevailingWindDecFeb` | string \| null | Преобладающее направление ветра дек—фев (С / ЮВ / …). |
| `maxWindJan` | number \| null | Максимальная скорость ветра за январь, м/с. |
| `meanWindLe8` | number \| null | Средняя скорость ветра при T ≤ 8 °C, м/с. |

### warmPeriod — тёплый период (СП 131.13330)

```json
"warmPeriod": {
  "barometric": 997,
  "temp095": 23.0,
  ...
}
```

| Поле | Тип | Описание |
|---|---|---|
| `barometric` | integer \| null | Барометрическое давление, гПа. |
| `temp095` | number \| null | T наиболее тёплого месяца с обеспеч. 0.95, °C. |
| `temp099` | number \| null | То же, обеспеч. 0.99. |
| `meanMaxTempWarmMonth` | number \| null | Средняя максимальная T наиболее тёплого месяца. |
| `absMax` | number \| null | Абсолютная максимальная T. |
| `dailyAmplitude` | number \| null | Средняя суточная амплитуда T тёплого месяца. |
| `humidityWarm` | integer \| null | Относительная влажность наиболее тёплого месяца, %. |
| `humidityWarm15` | integer \| null | То же в 15:00, %. |
| `precipAprOct` | integer \| null | Сумма осадков апрель—октябрь, мм. |
| `dailyMaxPrecip` | integer \| null | Максимальные суточные осадки, мм. |
| `prevailingWindJunAug` | string \| null | Преобладающее направление ветра июнь—август. |
| `minWindJul` | number \| null | Минимальная скорость ветра за июль, м/с. |

### monthlyTemps — среднемесячные температуры (СП 131.13330)

```json
"monthlyTemps": {
  "byMonth": [-7.0, -6.4, -1.0, 6.5, 13.3, 17.2, 19.2, 17.2, 11.4, 5.4, -0.7, -4.8],
  "yearly": 5.9,
  "source": "...",
  "status": "verified"
}
```

| Поле | Тип | Описание |
|---|---|---|
| `byMonth` | array(12) \| null | Средние T за январь … декабрь, °C. |
| `yearly` | number \| null | Среднегодовая T, °C. |

---

## Использование из TypeScript

В репо есть готовые функции:

```ts
import {
  getSettlementClimateById,
  searchSettlements,
  getAllSettlements,
} from "./types/climate";

const moscow = getSettlementClimateById("moscow");
// moscow.snow.sgKpa === 1.5
// moscow.wind.w0Kpa === 0.23
// moscow.coldPeriod?.tempColdest5days092 === -23

const matches = searchSettlements("челяб"); // SettlementClimateData[]
```

## Полная типизация

Все интерфейсы и enum определены в `src/types/climate.ts`:
- `SettlementClimateData` — главная запись
- `SnowClimateData`, `WindClimateData`, `IceClimateData`, `SeismicClimateData`, `TerrainClimateData`
- `FrostDepthData`, `ColdClimateData`, `WarmClimateData`, `MonthlyTempsData`
- `ClimateParameterStatus`, `SettlementDataStatus`, `TerrainType`
