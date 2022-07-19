- makedev
  ```c
  // maj, min must match kernel define:
  // https://www.kernel.org/doc/Documentation/admin-guide/devices.txt
  dev_t makedev(unsigned int maj, unsigned int min);
  
  // Eg: for /dev/kmsg
  makedev(1, 11)
  ```

- mknod
  ```c
  // creates a filesystem node (file, device
  // special file, or named pipe) named pathname, with attributes
  // specified by mode and dev.
  // 'dev' can be make by makedev()
  int mknod(const char *pathname, mode_t mode, dev_t dev);

  // Eg: create kmsg device file only current user(root) can rw.
  mknod("/dev/kmsg", S_IFCHR | 0600, makedev(1, 11))
  ```