from django.core.cache import cache
from .models import Property
import logging
from django_redis import get_redis_connection

def get_all_properties():
    # Try to get data from Redis
    properties = cache.get('all_properties')
    if properties is None:
        # If not in cache, query the database
        properties = list(Property.objects.all().values(
            'id', 'title', 'description', 'price', 'location', 'created_at'
        ))
        # Store the result in Redis for 1 hour (3600 seconds)
        cache.set('all_properties', properties, timeout=3600)
    return properties


logger = logging.getLogger(__name__)

def get_redis_cache_metrics():
    # Get Redis connection from django-redis
    conn = get_redis_connection("default")

    # Fetch Redis INFO
    info = conn.info()

    # Extract hits and misses
    hits = info.get("keyspace_hits", 0)
    misses = info.get("keyspace_misses", 0)
    total = hits + misses

    # Compute hit ratio
    hit_ratio = (hits / total) if total > 0 else None

    # Log the metrics
    logger.info(f"Redis Metrics — Hits: {hits}, Misses: {misses}, Hit Ratio: {hit_ratio}")

    return {
        "keyspace_hits": hits,
        "keyspace_misses": misses,
        "hit_ratio": hit_ratio,
    }
