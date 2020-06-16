# ohara-docs

* Building development environment
  * Install Hugo - https://gohugo.io/getting-started/installing/
  * For the preview and development, just type:
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
