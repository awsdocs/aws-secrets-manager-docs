# type CacheConfig<a name="retrieving-secrets_cache-go_CacheConfig"></a>

Cache configuration options for a [Cache](retrieving-secrets_cache-go_cache.md), such as maximum cache size, default [version stage](getting-started.md#term_version), and Time to Live \(TTL\) for cached secrets\.

```
type CacheConfig struct {

    // The maximum cache size. The default is 1024 secrets.
    MaxCacheSize int
            
    // The TTL of a cache item in nanoseconds. The default is 
    // 3.6e10^12 ns or 1 hour.
    CacheItemTTL int64
            
    // The version of secrets that you want to cache. The default 
    // is "AWSCURRENT".
    VersionStage string
            
    // Used to hook in-memory cache updates.
    Hook CacheHook
}
```