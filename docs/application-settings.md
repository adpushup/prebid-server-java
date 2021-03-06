# Application Settings
There are two ways to configure application settings: database and file. This document will describe both approaches.

## Account properties
- `id` - identifies publisher account.
- `price-granularity` - defines price granularity types: 'low','med','high','auto','dense','unknown'.
- `banner-cache-ttl` - how long (in seconds) banner will be available via the external Cache Service.
- `video-cache-ttl`- how long (in seconds) video creative will be available via the external Cache Service.
- `events-enabled` - enables events for account if true
- `enforce-ccpa` - enforces ccpa if true. Has higher priority than configuration in application.yaml.
- `gdpr.enabled` - enables gdpr verifications if true. Has higher priority than configuration in application.yaml.
- `gdpr.purposes.[p1-p10].enforce-purpose` - define type of enforcement confirmation: `no`/`basic`/`full`. Default `full`
- `gdpr.purposes.[p1-p10].enforce-vendors` - if equals to `true`, user must give consent to use vendors. Purposes will be omitted. Default `true`
- `gdpr.purposes.[p1-p10].vendor-exceptions[]` - bidder names that will be treated opposite to `pN.enforce-vendors` value.
- `gdpr.special-features.[f1-f2].enforce`- if equals to `true`, special feature will be enforced for purpose. Default `true`
- `gdpr.special-features.[f1-f2].vendor-exceptions` - bidder names that will be treated opposite to `sfN.enforce` value.
- `gdpr.purpose-one-treatment-interpretation` - option that allows to skip the Purpose one enforcement workflow. Values: ignore, no-access-allowed, access-allowed.
- `analytics-sampling-factor` - Analytics sampling factor value. 
- `truncate-target-attr` - Maximum targeting attributes size. Values between 1 and 255.

```
Purpose   | Purpose goal                    | Purpose meaning for PBS (n\a - not affected)  
----------|---------------------------------|---------------------------------------------
p1        | Access device                   | Stops usersync for given vendor and stops settings cookie on `/seuid`
p2        | Select basic ads                | Verify consent for each vendor as appropriate for the enforcement method before calling a bid adapter. If consent is not granted, log a metric and skip it.
p3        | Personalized ads profile        | n\a
p4        | Select personalized ads         | Verify consent for each vendor that passed the Purpose 2. If consent is not granted, remove the bidrequest.userId, user.ext.eids, user.ext.digitrust, device.if attributes and call the adapter.
p5        | Personalized content profile    | n\a
p6        | Select personalized content     | n\a
p7        | Measure ad performance          | Verify consent for each analytics module. If consent is not grantet skip it.
p8        | Measure content performance     | n\a
p9        | Generate audience insights      | n\a
p10       | Develop/improve products        | n\a

sf1       | Precise geo                     | Verifies user opt-in. If the user has opted out, rounds off the IP address and lat/long details 
sf2       | Fingerprinting                  | n\a
```

## File application setting

In file based approach all configuration stores in .yaml files, path to which are defined in application properties.

### Configuration in application.yaml

```
settings:
  filesystem:
    settings-filename: <directory to yaml file with settings>
```
### File format

```
accounts:
  - id: 14062
    bannerCacheTtl: 100
    videoCacheTtl: 100
    eventsEnabled: true
    priceGranularity: low
    enforceCcpa: true
    analyticsSamplingFactor: 1
    truncateTargetAttr: 40
    gdpr:
      enabled: true
      purposes:
        p1:
          enforce-purpose: full
          enforce-vendors: true
          vendor-exceptions:
            - bidder1
            - bidder2
        p2:
          enforce-purpose: full
          enforce-vendors: true
          vendor-exceptions:
            - bidder1
            - bidder2
        p3:
          enforce-purpose: full
          enforce-vendors: true
          vendor-exceptions:
            - bidder1
            - bidder2
        p4:
          enforce-purpose: full
          enforce-vendors: true
          vendor-exceptions:
            - bidder1
            - bidder2
        p5:
          enforce-purpose: full
          enforce-vendors: true
          vendor-exceptions:
            - bidder1
            - bidder2
        p6:
          enforce-purpose: full
          enforce-vendors: true
          vendor-exceptions:
            - bidder1
            - bidder2
        p7:
          enforce-purpose: full
          enforce-vendors: true
          vendor-exceptions:
            - bidder1
            - bidder2
        p8:
          enforce-purpose: full
          enforce-vendors: true
          vendor-exceptions:
            - bidder1
            - bidder2
        p9:
          enforce-purpose: full
          enforce-vendors: true
          vendor-exceptions:
            - bidder1
            - bidder2
        p10:
          enforce-purpose: full
          enforce-vendors: true
          vendor-exceptions:
            - bidder1
            - bidder2
      special-features:
        sf1:
          enforce: true
          vendor-exceptions:
            - bidder1
            - bidder2
        sf2:
          enforce: true
          vendor-exceptions:
            - bidder1
            - bidder2
      purpose-one-treatment-interpretation: ignore
```

  
## Database application setting

In database approach account properties are stored in database table with name accounts_account.

### Configuration in application.yaml
```
settings:
  database:
    type: <mysql or postgres>
    pool-size: 20
    type: mysql
    host: <host>
    port: <port>
```

### Table description 

Query to create accounts_account table:

```
'CREATE TABLE `accounts_account` (
`id` int(10) unsigned NOT NULL AUTO_INCREMENT,
`uuid` varchar(40) NOT NULL,
`price_granularity` enum('low','med','high','auto','dense','unknown') NOT NULL DEFAULT 'unknown',
`granularityMultiplier` decimal(9,3) DEFAULT NULL,
`banner_cache_ttl` int(11) DEFAULT NULL,
`video_cache_ttl` int(11) DEFAULT NULL,
`events_enabled` bit(1) DEFAULT NULL,
`enforce_ccpa` bit(1) DEFAULT NULL,
`enforce_gdpr` bit(1) DEFAULT NULL,
`tcf_config` json DEFAULT NULL,
`analytics_sampling_factor` tinyint(4) DEFAULT NULL,
`truncate_target_attr` tinyint(3) unsigned DEFAULT NULL,
`status` enum('active','inactive') DEFAULT 'active',
`updated_by` int(11) DEFAULT NULL,
`updated_by_user` varchar(64) DEFAULT NULL,
`updated` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY (`id`),
UNIQUE KEY `uuid` (`uuid`))
ENGINE=InnoDB AUTO_INCREMENT=1726 DEFAULT CHARSET=utf8'
```

where tcf_config column is json with next format

```
{
  "enabled": true,
  "purpose-one-treatment-interpretation": "ignore"
  "purposes": {
    "p1": {
      "enforce-purpose": "full",
      "enforce-vendors": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    },
    "p2": {
      "enforce-purpose": "full",
      "enforce-vendors": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    },
    "p3": {
      "enforce-purpose": "full",
      "enforce-vendors": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    },
    "p4": {
      "enforce-purpose": "full",
      "enforce-vendors": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    },
    "p5": {
      "enforce-purpose": "full",
      "enforce-vendors": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    },
    "p6": {
      "enforce-purpose": "full",
      "enforce-vendors": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    },
    "p7": {
      "enforce-purpose": "full",
      "enforce-vendors": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    },
    "p8": {
      "enforce-purpose": "full",
      "enforce-vendors": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    },
    "p9": {
      "enforce-purpose": "full",
      "enforce-vendors": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    },
    "p10": {
      "enforce-purpose": "full",
      "enforce-vendors": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    }
  },
  "special-features": {
    "sf1": {
      "enforce": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    },
    "sf2": {
      "enforce": true,
      "vendor-exceptions": [
        "bidder1",
        "bidder2"
      ]
    }
  }
}
```

Query used to get an account:
```
SELECT uuid, price_granularity, banner_cache_ttl, video_cache_ttl, events_enabled, enforce_ccpa, tcf_config, analytics_sampling_factor, truncate_target_attr 
FROM accounts_account where uuid = ?
LIMIT 1

```
