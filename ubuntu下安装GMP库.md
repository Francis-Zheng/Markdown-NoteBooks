教程链接1： [Ubuntu下安装和使用GMP](https://blog.csdn.net/win_in_action/article/details/46956245)


安装完成之后，即make install之后的提示：
/sbin/ldconfig.real: /usr/local/lib/libgmp.so.10 is not a symbolic link

----------------------------------------------------------------------
Libraries have been installed in:
   /usr/local/lib

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the '-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the 'LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the 'LD_RUN_PATH' environment variable
     during linking
   - use the '-Wl,-rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to '/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.

----------------------------------------------------------------------
 /bin/mkdir -p '/usr/local/include'
 /usr/bin/install -c -m 644 gmp.h '/usr/local/include'
make  install-data-hook
make[4]: Entering directory '/mnt/gmp/gmp-6.1.2'

+-------------------------------------------------------------+
| CAUTION:                                                    |
|                                                             |
| If you have not already run "make check", then we strongly  |
| recommend you do so.                                        |
|                                                             |
| GMP has been carefully tested by its authors, but compilers |
| are all too often released with serious bugs.  GMP tends to |
| explore interesting corners in compilers and has hit bugs   |
| on quite a few occasions.                                   |
|                                                             |
+-------------------------------------------------------------+

make[4]: Leaving directory '/mnt/gmp/gmp-6.1.2'
make[3]: Leaving directory '/mnt/gmp/gmp-6.1.2'
make[2]: Leaving directory '/mnt/gmp/gmp-6.1.2'
make[1]: Leaving directory '/mnt/gmp/gmp-6.1.2'

