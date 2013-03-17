---
layout: post
title: git 源代码里的一段
meta: 2013.3.17, 北京
---

Liuns这位C原教旨主义者，呵呵……

    typedef int (*open_istream_fn)(struct git_istream *,
                                   struct object_info *,
                                   const unsigned char *,
                                   enum object_type *);
    typedef int (*close_istream_fn)(struct git_istream *);
    typedef ssize_t (*read_istream_fn)(struct git_istream *, char *, size_t);

    struct stream_vtbl {
            close_istream_fn close;
            read_istream_fn read;
    };

    struct git_istream {
            const struct stream_vtbl *vtbl;
            unsigned long size; /* inflated size of full object */
            git_zstream z;
            enum { z_unused, z_used, z_done, z_error } z_state;

            union {
                    struct {
                            char *buf; /* from read_object() */
                            unsigned long read_ptr;
                    } incore;

                    struct {
                            void *mapped;
                            unsigned long mapsize;
                            char hdr[32];
                            int hdr_avail;
                            int hdr_used;
                    } loose;

                    struct {
                            struct packed_git *pack;
                            off_t pos;
                    } in_pack;

                    struct filtered_istream filtered;
            } u;
    };

