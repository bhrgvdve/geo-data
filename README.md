# Geo Data

Technology-independent geographic master data stored as plain JSON.

This repository is data-only. It does not include packages, schemas, database
dumps, APIs, framework files, tests, or build tooling.

## Contents

- Global geography master files for continents, subcontinents, regions,
  subregions, countries, states, and cities.
- Global reference files for languages, currencies, timezones, calling codes,
  and top-level domains.
- Per-country relationship files under `countries/{ISO2}/`.
- India-specific supplemental data under `countries/IN/`.

## Directory Structure

```text
geo-data/
  README.md
  LICENSE
  continents.json
  subcontinents.json
  regions.json
  subregions.json
  countries.json
  states.json
  cities.json
  languages.json
  currencies.json
  timezones.json
  calling_codes.json
  tlds.json
  countries/
    {ISO2}/
      currencies.json
      languages.json
      calling_codes.json
      tlds.json
      timezones.json
      region.json
      subregion.json
```

There are 250 country folders under `countries/`. Every country folder contains
the standard relationship files listed above. The `IN` folder also contains the
India-specific supplemental files.

## Dataset Summary

| File | Records | Purpose |
| --- | ---: | --- |
| `continents.json` | 7 | Continent master records. |
| `subcontinents.json` | 9 | Broad geographic grouping records linked to continents. |
| `regions.json` | 27 | Region records linked to continent and subcontinent IDs. |
| `subregions.json` | 27 | Subregion records linked to region IDs. |
| `countries.json` | 250 | Country identity and geography records. |
| `states.json` | 5,308 | First-level administrative divisions. |
| `cities.json` | 152,970 | Cities, towns, and places with hierarchy references. |
| `languages.json` | 153 | Language master records. |
| `currencies.json` | 164 | Currency master records. |
| `timezones.json` | 426 | Timezone master records. |
| `calling_codes.json` | 701 | Calling-code records linked to countries. |
| `tlds.json` | 278 | Top-level domain records. |

## Geographic Hierarchy

```text
continents.json
  -> subcontinents.json
    -> regions.json
      -> subregions.json
        -> countries.json
          -> states.json
            -> cities.json
```

Relationship fields use numeric IDs:

```text
continent_id
subcontinent_id
region_id
subregion_id
country_id
state_id
city_id
```

The upstream country metadata provides reliable `region` and `subregion` names,
but not a third distinct subdivision below subregion. To keep the model complete
without inventing geography, `regions.json` and `subregions.json` both preserve
the source subregion level where no finer level is available.

## Global Files

- `continents.json`: fields `id`, `name`, `code`.
- `subcontinents.json`: fields `id`, `continent_id`, `name`, `code`, `slug`.
- `regions.json`: fields `id`, `continent_id`, `subcontinent_id`, `name`,
  `code`, `slug`.
- `subregions.json`: fields `id`, `continent_id`, `subcontinent_id`,
  `region_id`, `name`, `code`, `slug`.
- `countries.json`: fields `id`, `continent_id`, `subcontinent_id`,
  `region_id`, `subregion_id`, `name`, `official_name`, `common_name`, `iso2`,
  `iso3`, `numeric_code`, `currency_id`, `latitude`, `longitude`.
- `states.json`: fields `id`, `continent_id`, `subcontinent_id`, `region_id`,
  `subregion_id`, `country_id`, `name`, `code`, `type`.
- `cities.json`: fields `id`, `continent_id`, `subcontinent_id`, `region_id`,
  `subregion_id`, `country_id`, `state_id`, `name`, `latitude`, `longitude`,
  `timezone_id`, `is_country_capital`, `is_state_capital`.
- `languages.json`: fields `id`, `name`, `iso639_3`, `rtl`.
- `currencies.json`: fields `id`, `code`, `name`, `symbol`,
  `thousand_separator`, `decimal_separator`.
- `timezones.json`: fields `id`, `name`, `utc_offset`.
- `calling_codes.json`: fields `id`, `country_id`, `code`.
- `tlds.json`: fields `id`, `tld`.

## Countries

`countries.json` is the master source for country IDs. It does not store capital
names, language arrays, calling-code arrays, TLD arrays, timezone arrays, or
region/subregion names. Those relationships are represented by IDs and
country-specific relationship files.

Example:

```json
{
  "id": 91,
  "continent_id": 3,
  "subcontinent_id": 3,
  "region_id": 11,
  "subregion_id": 11,
  "name": "India",
  "official_name": "Republic of India",
  "common_name": "India",
  "iso2": "IN",
  "iso3": "IND",
  "numeric_code": 356,
  "currency_id": 65,
  "latitude": 20,
  "longitude": 77
}
```

## Country Relationship Files

Each country has a folder under `countries/{ISO2}/`. The folder name is for
organization; relationships inside JSON use numeric IDs.

Standard files:

- `currencies.json`: currency relationships with `currency_id` and `is_primary`.
- `languages.json`: language relationships with `language_id`, `is_official`,
  and `is_primary`.
- `calling_codes.json`: calling-code relationships with `calling_code_id` and
  `is_primary`.
- `tlds.json`: top-level-domain relationships with `tld_id` and `is_primary`.
- `timezones.json`: timezone relationships with `timezone_id` and `is_primary`.
- `region.json`: country region relationship with `region_id`.
- `subregion.json`: country subregion relationship with `subregion_id`.

Examples:

```json
[
  {
    "currency_id": 65,
    "is_primary": true
  }
]
```

```json
[
  {
    "language_id": 32,
    "is_official": true,
    "is_primary": true
  }
]
```

## Cities And Capitals

`cities.json` contains full geographic references and capital flags:

```json
{
  "id": 133210,
  "continent_id": 3,
  "subcontinent_id": 3,
  "region_id": 11,
  "subregion_id": 11,
  "country_id": 91,
  "state_id": 109,
  "name": "New Delhi",
  "latitude": 28.63576,
  "longitude": 77.22445,
  "timezone_id": 252,
  "is_country_capital": true,
  "is_state_capital": false
}
```

Country capitals are marked only when the country capital name matched an
existing city record in the same country. State/province/admin-division capitals
are not guessed; `is_state_capital` remains `false` where authoritative source
data is unavailable.

## India Data

India-specific files contain additional India-focused data that does not belong
in the global base files. They are stored in `countries/IN/`.

| File | Records | Purpose |
| --- | ---: | --- |
| `countries/IN/districts.json` | 136 | India district/admin-level records available from the source. |
| `countries/IN/postal_codes.json` | 19,095 | India PIN/postal records. |
| `countries/IN/rto_codes.json` | 0 | Placeholder until a reliable redistributable RTO source is added. |
| `countries/IN/other-useful-data.json` | 1 | Notes about India-specific source limitations. |

India uses `country_id: 91` consistently across all files.
