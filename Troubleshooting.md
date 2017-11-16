## Generating debugging logs

For most common issues, debugging logs can be generated and can help us root cause the problem.\
In order to generate these:
* Edit your runtime configuration under `/etc/nvidia-container-runtime/config.toml` and
uncomment the `debug=...` line.
* Run your container again, thus reproducing the issue and generating the logs.

## Generating core dumps

In the event of a critical failure, core dumps can be automatically generated and can help us troubleshoot issues.\
Refer to [core(5)](http://man7.org/linux/man-pages/man5/core.5.html) in order to generate these, in particular make sure that:
* `/proc/sys/kernel/core_pattern` is correctly set and points somewhere with write access
* `ulimit -c` is set to a sensible default

In case the `nvidia-container-cli` process becomes unresponsive, [gcore(1)](http://man7.org/linux/man-pages/man1/gcore.1.html) can also be used.

## Sharing your debugging information

You can attach a particular output to your issue with a [drag and drop](https://help.github.com/articles/file-attachments-on-issues-and-pull-requests/) into the comment section.