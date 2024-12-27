Some findings for PS4 PKG's and information i gathered around.

# PKG General Info

***

A PS4 PKG looks like this: 

![46883920-5b8cd900-ce21-11e8-872c-ecfcda752aba](https://user-images.githubusercontent.com/4313123/221126368-e20f1b7a-0550-41c4-b847-f1fd46c7ed0a.png)

First 4.096 bytes (0x1000) are the PKG Header. PKG header appears to be in Big Endian (like it was in ps3) although PS4 generally is a Small Endian machine.

***

# PKG Header

| Field                   | Type | Size | Offset | Description
| ----------------------- | -----|----- |------- |------------
| pkg_magic               | U32  |    4 | 0x000  | Should be 0x7F434E54 to be PS4 valid
| pkg_flags               | U32  |    4 | 0x004  | [Check PKG Flags table below](https://github.com/georgemoralis/shadPS4/wiki/PKG-Information#pkg-flags-table)
| 0x8                     | U32  |    4 | 0x008  | ???
| pkg_file_count          | U32  |    4 | 0x00C  | 
| pkg_table_entry_count   | U32  |    4 | 0x010  | Number of files inside PKG (doesn't include PFS)
| pkg_sc_entry_count      | U16  |    2 | 0x014  |
| pkg_table_entry_count_2 | U16  |    2 | 0x016  | same as pkg_table_entry_count   
| pkg_table_entry_offset  | U32  |    4 | 0x018  | offset of the first pkg file entry [Check File Table below](https://github.com/georgemoralis/shadPS4/wiki/PKG-Information#pkg-file-table)
| pkg_sc_entry_data_size  | U32  |
| pkg_body_offset         | U64|
| pkg_body_size           | U64|
| pkg_content_offset      | U64|
| pkg_content_size        | U64|
| pkg_content_id          | U08|
| pkg_padding             | U08|
| pkg_drm_type            | U32|
| pkg_content_type        | U32|
| pkg_content_flags       | U32|
| pkg_promote_size        | U32|
| pkg_version_date        | U32|
| pkg_version_hash        | U32|
| pkg_0x088               | U32|
| pkg_0x08C               | U32|
| pkg_0x090               | U32|
| pkg_0x094               | U32|
| pkg_iro_tag             | U32|
| pkg_drm_type_version    | U32|
| pkg_zeroes_1            | U08|
| digest_entries1         | U08|
| digest_entries2         | U08|
| digest_table_digest     | U08|
| digest_body_digest      | U08|
| pkg_zeroes_2            | U08|
| pkg_0x400               | U32|
| pfs_image_count         | U32|
| pfs_image_flags         | U64|
| pfs_image_offset        | U64|
| pfs_image_size          | U64|
| pkg_size                | U64|
| pfs_signed_size         | U32|
| pfs_cache_size          | U32|
| pfs_image_digest        | U08|
| pfs_signed_digest       | U08|
| pfs_split_size_nth_0    | U64|
| pfs_split_size_nth_1    | U64|
| pkg_zeroes_3            | U08|
| pkg_digest              | U08|

## PKG Flags Table

| Name      | Value     | Description
| --------- | --------- | ----------- 
| UNKNOWN   | 0x01      |
| VER_1     | 0x01000000|
| VER_2     | 0x02000000|
| INTERNAL  | 0x40000000|
| FINALIZED | 0x80000000|

# PKG File Table

The file table is a list of file entries. File entries start from **pkg_table_entry_offset** field of pkg header and it's increasing by 0x20 for each file entry . **pkg_table_entry_count** field of pkg header indicates the number of file entries. File Table values are **also Big Endian**

| Field           | Type | Size | Offset | Description
| --------------  | -----|----- |-----   |------------|
| id              | U32  |    4 | 0x000  | File ID, useful for files without a filename entry.[Check ID to filename Table below](https://github.com/georgemoralis/shadPS4/wiki/PKG-Information#id-to-filename-table)
| filename_offset | U32  |    4 | 0x004  | Offset into the filenames table (ID 0x200) where this file's name is located
| flags1          | U32  |    4 | 0x008  | Flags including encrypted flag, etc
| flags2          | U32  |    4 | 0x00C  | Flags including encryption key index, etc
| offset          | U32  |    4 | 0x010  | Offset into PKG to find the file
| size            | U32  |    4 | 0x014  | Size of the file
| padding         | U64  |    8 | 0x018  | blank padding

## ID to Filename Table

This table matches the id of File Table with filename

| Id     | Filename                   | Extra info
| ------ | -------------------------- |------------
| 0x0001 | digests                    |
| 0x0010 | entry_keys                 |
| 0x0020 | image_key                  |
| 0x0080 | general_digests            |
| 0x0100 | metas                      |
| 0x0200 | entry_names                |
| 0x0400 | license.dat                |
| 0x0401 | license.info               |
| 0x0402 | nptitle.dat                |
| 0x0403 | npbind.dat                 | Contains NPCommID and some other info (???)
| 0x0409 | psreserved.dat             |
| 0x1000 | param.sfo                  | contains information critical to the app / game
| 0x1001 | playgo-chunk.dat           | contains data regarding playgo
| 0x1002 | playgo-chunk.sha           | contains hash of playgo
| 0x1003 | playgo-manifest.xml        | contains manifest for playgo
| 0x1004 | pronunciation.xml          | contains word definitions for PS4's voice recognition software
| 0x1005 | pronunciation.sig          | signature of definition file
| 0x1006 | pic1.png                   | large game preview icon
| 0x1007 | pubtoolinfo.dat            |
| 0x100B | shareparam.json            |
| 0x100C | shareoverlayimage.png      |
| 0x100E | shareprivacyguardimage.png |
| 0x1200 | icon0.png                  | small icon
| 0x1220 | pic0.png                   | small game preview icon
| 0x1240 | snd0.at9                   |
| 0x1280 | icon0.dds                  |
| 0x12A0 | pic0.dds                   |
| 0x12C0 | pic1.dds                   | 
| 0x1400 | trophy/trophy00.trp        |