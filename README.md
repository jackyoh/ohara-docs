# ohara-docs

* Building development environment
  * Install Hugo - https://gohugo.io/getting-started/installing/
  * Preview ohara-docs in localhost:
    ```
    [ohara-docs]$ hugo server [-D] [--disableFastRender] [--bind=0.0.0.0]
    ```
    * `-D`: include content marked as draft
    * `--disableFastRender`: enables full re-renders on changes
    * `--bind`: interface to which the server will bind(default "127.0.0.1")
