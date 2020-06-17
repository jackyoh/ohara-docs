# ohara-docs

This repository build with [Hugo](https://gohugo.io/) and [Academic](https://sourcethemes.com/academic/)
and use Github Pages to host [OharaStream](https://oharastream.github.io) website.

## Building development environment

* If you are newbie to **Hugo**, [this](https://gohugo.io/getting-started/) would be a great getting started.
* If you are already installed Hugo, for the preview **ohara-docs** from local, just type:
  ```
  [ohara-docs]$ ./view.sh
  ```
* You alse can use Hugo command directly, for example:
  ```
  [ohara-docs]$ hugo server [-D] [--disableFastRender] [--bind=0.0.0.0]
  ```
  * `-D`: include content marked as draft
  * `--disableFastRender`: enables full re-renders on changes
  * `--bind`: interface to which the server will bind(default "127.0.0.1")
