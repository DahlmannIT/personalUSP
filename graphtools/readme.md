## File aus dem Docker Container speichern

* ```sh
  sudo docker cp agitated_moore:/home/user/two-nodes.png /home/jay
  ```
   - `agitated_moore` is the name of the container (check with `sudo docker ps -a`
   - `:/home/user/` is the directory inside the container
   - `two-nodes.png` is the file inside the container 
   - `/home/jay` is the directory outside where it should be copied to
