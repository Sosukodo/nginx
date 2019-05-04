## Compiling and Installing from Source
Compiling NGINX Open Source from source affords more flexibility than prebuilt packages: you can add particular modules (from NGINX or third parties), and apply latest security patches.

	$ git clone https://github.com/Sosukodo/nginx.git
	$ cd nginx

### Installing NGINX Dependencies
Prior to compiling NGINX Open Source from source, you need to install libraries for its dependencies:

	$ cd nginx/deps

#### PCRE
*Required by the NGINX Core and Rewrite modules.*

    $ tar -zxf pcre-8.42.tar.gz
    $ cd pcre-8.42
    $ ./configure
    $ make -j4
    $ sudo make install

##### zlib
*Required by the NGINX Gzip module.*

    $ tar -zxf zlib-1.2.11.tar.gz
    $ cd zlib-1.2.11
    $ ./configure
    $ make -j4
    $ sudo make install

##### OpenSSL
*Required by the NGINX SSL module and others.*

    $ tar -zxf openssl-1.1.1b.tar.gz
    $ cd openssl-1.1.1b
    $ ./config --prefix=/usr
    $ make -j4
    $ sudo make install

After all dependencies are built and installed, cd into your nginx root directory:

	$ cd ../

### Configuring the Build Options

Configure options are specified with the ./configure script that sets up various NGINX parameters, including paths to source and configuration files, compiler options, connection processing methods, and the list of modules. The script finishes by creating the Makefile required to compile the code and install NGINX Open Source.

#### Example Configuration
*The following is a good **default** configuration*

`$ ./configure
--sbin-path=/usr/local/nginx/nginx
--conf-path=/usr/local/nginx/nginx.conf
--pid-path=/usr/local/nginx/nginx.pid
--with-pcre=deps/pcre-8.42
--with-zlib=deps/zlib-1.2.11
--with-http_ssl_module`

*The following is a good **lite** configuration*

`$ ./configure
--sbin-path=/usr/local/nginx/nginx
--conf-path=/usr/local/nginx/nginx.conf
--pid-path=/usr/local/nginx/nginx.pid
--with-pcre=deps/pcre-8.42
--with-zlib=deps/zlib-1.2.11
--with-http_ssl_module
--without-http_auth_basic_module
--without-http_browser_module
--without-http_empty_gif_module
--without-http_referer_module
--without-http_scgi_module
--without-http_ssi_module
--without-http_split_clients_module
--without-http_userid_module
--without-http_uwsgi_module`

### Configuring NGINX Paths

The configure script allows you to set paths to NGINX binary and configuration files, and to dependent libraries such as PCRE or SSL, in order to link them statically to the NGINX binary.

#### Parameter Descriptions

`--prefix=<PATH>` Directory for NGINX files, and the base location for all relative paths set by the other configure script options (excluding paths to libraries) and for the path to the nginx.conf configuration file. Default: `/usr/local/nginx`.

`--sbin-path=<PATH>` Name of the NGINX executable file, which is used only during installation. Default: `<prefix>/sbin/nginx`

`--conf-path=<PATH>` Name of the NGINX configuration file. You can, however, always override this value at startup by specifying a different file with the -c <FILENAME> option on the nginx command line. Default: `<prefix>/conf/nginx.conf`

`--pid-path=<PATH>` Name of the nginx.pid file, which stores the process ID of the nginx master process. After installation, the path to the filename can be changed with the pid directive in the NGINX configuration file. Default: `<prefix>/logs/nginx.pid`

`--error-log-path=<PATH>` Name of the primary log file for errors, warnings, and diagnostic data. After installation, the filename can be changed with the the error_log directive in the NGINX configuration file. Default:` <prefix>/logs/error.log`

`--http-log-path=<PATH>` Name of the primary log file for requests to the HTTP server. After installation, the filename can always be changed with the access_log directive in the NGINX configuration file. Default:` <prefix>/logs/access.log`

`--user=<NAME>` Name of the unprivileged user whose credentials are used by the NGINX worker processes. After installation, the name can be changed with the user directive in the NGINX configuration file. Default: `nobody`

`--group=<NAME>` Name of the group whose credentials are used by the NGINX worker processes. After installation, the name can be changed with the user directive in the NGINX configuration file. Default: the value set by the --user option.

`--with-pcre=<PATH>` Path to the source for the PCRE library, which is required for regular expressions support in the location directive and the Rewrite module.

`--with-pcre-jit` Builds the PCRE library with “just-in-time compilation” support (the pcre_jit directive).

`--with-zlib=<PATH>` Path to the source for the zlib library, which is required by the Gzip module.

### Configuring NGINX GCC Options

With the configure script you can also specify compiler‑related options.

#### Parameter Descriptions

`--with-cc-opt="<PARAMETERS>"` Additional parameters that are added to the CFLAGS variable. When using the system PCRE library under FreeBSD, the mandatory value is --with-cc-opt="-I /usr/local/include". If the number of files supported by select() needs to be increased, it can also specified here as in this example: --with-cc-opt="-D FD_SETSIZE=2048".

`--with-ld-opt="<PARAMETERS>"` Additional parameters that are used during linking. When using the system PCRE library under FreeBSD, the mandatory value is --with-ld-opt="-L /usr/local/lib".

### Specifying NGINX Connection Processing Methods

With the configure script you can redefine the method for event‑based polling. For more information, see Connection processing methods in the NGINX reference documentation.

#### Module Name Descriptions

`--with-select_module`, `--without-select_module` Enables or disables building a module that enable NGINX to work with the select() method. The modules is built automatically if the platform does not appear to support more appropriate methods such as kqueue, epoll, or /dev/poll.

`--with-poll_module`, `--without-poll_module` Enables or disables building a module that enables NGINX to work with the poll() method. The module is built automatically if the platform does not appear to support more appropriate methods such as kqueue, epoll, or /dev/poll.

### Selecting the NGINX Modules to Build

NGINX consists of a set of function‑specific modules, which are specified with configure script along with other build options.

Some modules are built by default – they do not have to be specified with the configure script. Default modules can however be explicitly excluded from the NGINX binary with the `--without-<MODULE-NAME>` option on the configure script.

Modules not included by default, as well as third‑party modules, must be explicitly specified in the configure script together with other build options. Such modules can be linked to NGINX binary either statically (they are then loaded each time NGINX starts) or dynamically (they are loaded only if associated directives are included in the NGINX configuration file.

### Modules Built by Default

If you do not need a module that is built by default, you can disable it by naming it with the `--without-<MODULE-NAME>` option on the configure script.

#### Example
*This example disables the Empty GIF module*

`$ ./configure
--sbin-path=/usr/local/nginx/nginx
--conf-path=/usr/local/nginx/nginx.conf
--pid-path=/usr/local/nginx/nginx.pid
--with-http_ssl_module
--with-stream
--with-pcre=../pcre-8.42
--with-zlib=../zlib-1.2.11
--without-http_empty_gif_module`

#### Module Name Descriptions

**http_access_module** Accepts or denies requests from specified client addresses.

**http_auth_basic_module** Limits access to resources by validating the user name and password using the HTTP Basic Authentication protocol.

**http_autoindex_module** Processes requests ending with the forward-slash character (/) and produces a directory listing.

**http_browser_module** Creates variables whose values depend on the value of the User-Agent request header.

**http_charset_module** Adds the specified character set to the Content-Type response header. Can convert data from one character set to another.

**http_empty_gif_module** Emits a single-pixel transparent GIF.

**http_fastcgi_module** Passes requests to a FastCGI server.

**http_geo_module** Creates variables with values that depend on the client IP address.

**http_gzip_module** Compresses responses using gzip, reducing the amount of transmitted data by half or more.

**http_limit_conn_module** Limits the number of connections per a defined key, in particular, the number of connections from a single IP address.

**http_limit_req_module** Limits the request processing rate per a defined key, in particular, the processing rate of requests coming from a single IP address.
http_map_module Creates variables whose values depend on the values of other variables.

**http_memcached_module** Passes requests to a memcached server.

**http_proxy_module** Passes HTTP requests to another server.

**http_referer_module** Blocks requests with invalid values in the Referer header.

**http_rewrite_module** Changes the request URI using regular expressions and return redirects; conditionally selects configurations. Requires the PCRE library.

**http_scgi_module** Passes requests to an SCGI server.

**http_ssi_module** Processes SSI (Server Side Includes) commands in responses passing through it.

**http_split_clients_module** Creates variables suitable for A/B testing, also known as split testing.

**http_upstream_hash_module** Enables the generic Hash load-balancing method.

**http_upstream_ip_hash_module** Enables the IP Hash load-balancing method.

**http_upstream_keepalive_module** Enables keepalive connections.

**http_upstream_least_conn_module** Enables the Least Connections load-balancing method.

**http_upstream_zone_module** Enables shared memory zones.

**http_userid_module** Sets cookies suitable for client identification.

http_uwsgi_module Passes requests to a uwsgi server.

#### Including Modules Not Built by Default

Many NGINX modules are not built by default, and must be listed on the configure command line to be built.

The mail, stream, geoip, image_filter, perl and xslt modules can be compiled as dynamic. See Dynamic Modules for details.

An example of the configure command that includes nondefault modules:

`$ ./configure
--sbin-path=/usr/local/nginx/nginx
--conf-path=/usr/local/nginx/nginx.conf
--pid-path=/usr/local/nginx/nginx.pid
--with-pcre=../pcre-8.42
--with-zlib=../zlib-1.2.11
--with-http_ssl_module
--with-stream
--with-mail`

#### Module Name Descriptions

`--with-cpp_test_module` Tests the C++ compatibility of header files.

`--with-debug` Enables the debugging log.

`--with-file-aio` Enables asynchronous I/O.

`--with-google_perftools_module` Allows using Google Performance tools library.

`--with-http_addition_module` Adds text before and after a response.

`--with-http_auth_request_module` Implements client authorization based on the result of a subrequest.

`--with-http_dav_module` Enables file management automation using the WebDAV protocol.

`--with-http_degradation_module` Allows returning an error when a memory size exceeds the defined value.

`--with-http_flv_module` Provides pseudo-streaming server-side support for Flash Video (FLV) files.

`--with-http_geoip_module` Enables creating variables whose values depend on the client IP address. The module uses MaxMind GeoIP databases. To compile as a separate dynamic module instead, change the option to –with-http_geoip_module=dynamic.

`--with-http_gunzip_module` Decompresses responses with Content-Encoding: gzip for clients that do not support the _zip_ encoding method.

`--with-http_gzip_static_module` Allows sending precompressed files with the .gz filename extension instead of regular files.

`--with-http_image_filter_module` Transforms images in JPEG, GIF, and PNG formats. The module requires the LibGD library. To compile as a separate dynamic module instead, change the option to --with-http_image_filter_module=dynamic.

`--with-http_mp4_module` Provides pseudo-streaming server-side support for MP4 files.

`--with-http_perl_module` Used to implement location and variable handlers in Perl and insert Perl calls into SSI. Requires the PERL library. To compile as a separate dynamic module instead, change the option to --with-http_perl_module=dynamic.

`--with-http_random_index_module` Processes requests ending with the slash character (‘/’) and picks a random file in a directory to serve as an index file.

`--with-http_realip_module` Changes the client address to the one sent in the specified header field.

`--with-http_secure_link_module` Used to check authenticity of requested links, protect resources from unauthorized access, and limit link lifetime.

`--with-http_slice_module` Allows splitting a request into subrequests, each subrequest returns a certain range of response. Provides more effective caching of large files.

`--with-http_ssl_module` Enables HTTPS support. Requires an SSL library such as OpenSSL.

`--with-http_stub_status_module` Provides access to basic status information. Note that NGINX Plus customers do not require this module as they are already provided with extended status metrics and interactive dashboard.

`--with-http_sub_module` Modifies a response by replacing one specified string by another.

`--with-http_xslt_module` Transforms XML responses using one or more XSLT stylesheets. The module requires the Libxml2 and XSLT libraries. To compile as a separate dynamic module instead, change the option to --with-http_xslt_module=dynamic.

`--with-http_v2_module` Enable support for HTTP/2. See The HTTP/2 Module in NGINX on the NGINX blog for details.

`--with-mail` Enables mail proxy functionality. To compile as a separate dynamic module instead, change the option to --with-mail=dynamic.

`--with-mail_ssl_module` Provides support for a mail proxy server to work with the SSL/TLS protocol. Requires an SSL library such as OpenSSL.

`--with-stream` Enables the TCP and UDP proxy functionality. To compile as a separate dynamic module instead, change the option to --with-stream=dynamic.

`--with-stream_ssl_module` Provides support for a stream proxy server to work with the SSL/TLS protocol. Requires an SSL library such as OpenSSL.

`--with-threads` Enables NGINX to use thread pools. For details, see Thread Pools in NGINX Boost Performance 9x! on the NGINX blog.

### Including Third-Party Modules

You can extend NGINX functionality by compiling NGINX Open Source with your own module or a third‑party module. Some third‑party modules are listed in the NGINX Wiki. Use third‑party modules at your own risk as their stability is not guaranteed.

#### Statically Linked Modules

Most modules built into NGINX Open Source are statically linked: they are built into NGINX Open Source at compile time and are linked to the NGINX binary statically. These modules can be disabled only by recompiling NGINX.

To compile NGINX Open Source with a statically linked third‑party module, include the` --add-module=<PATH>` option on the configure command, where <PATH> is the path to the source code (this example is for the RTMP module):

`$  ./configure ... --add-module=/usr/build/nginx-rtmp-module`

#### Dynamically Linked Modules

NGINX modules can also be compiled as a shared object (\*.so file) and then dynamically loaded into NGINX Open Source at runtime. This provides more flexibility, as the module can be loaded or unloaded at any time by adding or removing the associated load_module directive in the NGINX configuration file and reloading the configuration. Note that the module itself must support dynamic linking.

To compile NGINX Open Source with a dynamically loaded third‑party module, include the --add-dynamic-module=<PATH> option on the configure command, where `<PATH>` is the path to the source code:

`$  ./configure ... --add-dynamic-module=<PATH>`

The resulting \*.so files are written to the the prefix/modules/ directory, where the prefix is a directory for server files such as /usr/local/nginx/.

To load a dynamic module, add the load_module directive to the NGINX configuration after installation:

`load_module modules/ngx_mail_module.so;`

For more information, see Compiling Third‑Party Dynamic Modules for NGINX and NGINX Plus on the NGINX blog and Extending NGINX in the Wiki.

### Completing the Installation from Source

##### Compile and install the build:

    $ make
    $ sudo make install

After the installation is finished, start NGINX Open Source:

    $ sudo nginx
