关于cmake的链接，默认在add_library和target_link_libraries就都用库名就好
对于复杂的项目，对于共享库可以用set_target_properties 重命名OUTPUT_NAME，可以不用加有-Wl,-soname -Wl 指出链接标识，默认不会报错。但是为了进行库的版本控制，可以添加这个参数，例如 -Wl,-soname -Wl,libfoo.so.1.0。