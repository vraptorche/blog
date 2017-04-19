```java
package co.jware.sojka.entities.core.metasearch

import co.jware.sojka.core.entities.listeners.MetaLinkListener
import co.jware.sojka.entities.Persistent
import co.jware.sojka.entities.core.geo.GeoLocationEntity
import org.hibernate.annotations.ColumnDefault
import org.hibernate.search.annotations.*

import javax.persistence.*
import java.time.ZonedDateTime

@Entity
@EntityListeners(MetaLinkListener)
@Table(name = "meta_link")
@Inheritance(strategy = InheritanceType.JOINED)
@Indexed(index = 'meta_link')
class MetaLinkEntity extends Persistent {

    @Column(name = "base_uri")
    String baseUri;
    @Column(length = 1_000, unique = true)
    String href
    String title
    String company
    String address

    @Column(length = 1_000)
    @Field()
    String summary;

    @ElementCollection()
    @CollectionTable(name = 'meta_link_source',
            joinColumns = @JoinColumn(name = 'meta_link_uuid'))
    @Column(name = 'source', length = 96)
    Set<String> sources = []

    @ElementCollection
    @CollectionTable(name = 'meta_link_keywords',
            joinColumns = @JoinColumn(name = 'meta_link_uuid'))
    @Column(name = 'keyword', length = 128)
    @IndexedEmbedded()
    @Field
    Set<String> keywords

    @ElementCollection
    @CollectionTable(name = 'meta_link_locations',
            joinColumns = @JoinColumn(name = 'meta_link_uuid'))
    @Column(name = 'location', length = 128)
    @IndexedEmbedded()
    @Field
    Set<String> locations

    @ManyToMany()
    @JoinTable(name = 'meta_link_geo_locations',
            joinColumns = @JoinColumn(name = 'meta_link_uuid',foreignKey = @ForeignKey()),
            inverseJoinColumns = @JoinColumn(name = 'geo_location_uuid'))
    @IndexedEmbedded()
    Set<GeoLocationEntity> geoLocations

    @ColumnDefault("200")
    @Column(name = "http_status")
    int httpStatus

    @Column(updatable = false)
    ZonedDateTime created = ZonedDateTime.now();

    @Field
    @DateBridge(resolution = Resolution.MILLISECOND)
    @SortableField()
    ZonedDateTime updated;
}
```