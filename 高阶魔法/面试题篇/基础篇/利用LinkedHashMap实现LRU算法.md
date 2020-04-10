```java
public class LRUCache extends LinkedHashMap {
 	private final int CACHE_SIZE;
    
    public LRUCache(int cacheSize) {
        super( (int) Math.ceil(cacheSize / 0.75) + 1, 0.75f, true);
        CACHE_SIZE = cacheSize;
    }
    
    @Override
    protected boolean removeEldestEntry(Map.Entry eldest) {
        return size() > CACHE_SIZE;
    }
    
}
```

