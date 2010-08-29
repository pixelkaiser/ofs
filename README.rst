OFS is a bucket/object storage library.

It provides a common API for doing storing bitstreams (+ a bit of related
metadata) in 'bucket/object' stores such as:

  * Filesystem (+ pairtree)
  * S3
  * Archive.org
  * Riak (in progress)
  * **add a backend here**

Why use the library:

  * Abstraction: write common code but use different storage backends
  * More than a filesystem, less than a database - support for metadata as well
    bitstreams
  * Extra features:

      * Sharding - automatic sharding of files to support distributed file-system like structure (planned)


Example Usage
=============

(local version - depends on 'pairtree', and 'simplejson')::

    >>> from ofs.local import OFS

    >>> o = OFS()
    (Equivalent to 'o = OFS(storage_dir = "data", uri_base="urn:uuid:", hashing_type="md5")')

    # Claim a bucket - this will add the bucket to the list of existing ones
    >>> uuid_id = o.claim_a_bucket()
    >>> uuid_id
    '4aaa43cdf5ba44e2ad25acdbd1cf2f70'

    # Choose a bucket name - if it exists, a new UUID one will be formed instead and returned
    >>> bucket_id = o.claim_a_bucket("foo")
    >>> bucket_id
    'foo'
    >>> bucket_id = o.claim_a_bucket("foo")
    >>> bucket_id
    '1bf93208521545879e79c13614cd12f0'

    # Store a file:
    >>> o.put_stream(bucket_id, "foo.txt", open("foo....))
    {'_label': 'foo.txt', '_content_length': 10, '_checksum': 'md5:10feda25f8da2e2ebfbe646eea351224', '_last_modified': '2010-08-02T11:37:21', '_creation_date': '2010-08-02T11:37:21'}

    # or:
    >>> o.put_stream(bucket_id, "foo.txt", "asidaisdiasjdiajsidjasidji")
    {'_label': 'foo.txt', '_content_length': 10, '_checksum': 'md5:10feda25f8da2e2ebfbe646eea351224', '_last_modified': '2010-08-02T11:37:21', '_creation_date': '2010-08-02T11:37:21'}

    # adding a file with some parameters:
    >>> o.put_stream(bucket_id, "foooo", "asidaisdiasjdiajsidjasidji", params={"original_uri":"http://...."})
    {'_label': 'foooo', 'original_uri': 'http://....', '_last_modified': '2010-08-02T11:39:11', '_checksum': 'md5:3d690d7e0f4479c5a7038b8a4572d0fe', '_creation_date': '2010-08-02T11:39:11', '_content_length': 26}

    # adding to existing metadata:
    >>> o.update_metadata(bucket_id, "foooo", {'foo':'bar'})
    {'_label': 'foooo', 'original_uri': 'http://....', '_last_modified': '2010-08-02T11:39:11', '_checksum': 'md5:3d690d7e0f4479c5a7038b8a4572d0fe', '_creation_date': '2010-08-02T11:39:11', '_content_length': 26, 'foo': 'bar'}

    # Remove keys
    >>> o.remove_metadata_keys(bucket_id, "foooo", ['foo'])
    {'_label': 'foooo', 'original_uri': 'http://....', '_last_modified': '2010-08-02T11:39:11', '_checksum': 'md5:3d690d7e0f4479c5a7038b8a4572d0fe', '_creation_date': '2010-08-02T11:39:11', '_content_length': 26}

    # Delete blob
    >>> o.exists(bucket_id, "foooo")
    True
    >>> o.del_stream(bucket_id, "foooo")
    >>> o.exists(bucket_id, "foooo")
    False

    # Iterate through ids for buckets held:
    >>> for item in o.list_buckets():
    ...   print item
    ... 
    447536aa0f1b411089d12399738ede8e
    4a726b0a33974480a2a26d34fa0d494d
    4aaa43cdf5ba44e2ad25acdbd1cf2f70
    .... etc

