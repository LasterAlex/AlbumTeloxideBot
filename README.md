# An example of how to handle Albums in teloxide 0.12

All the source code is in ./src

To run it just make .env file in the root directory, using .env.example as a template., and run `cargo run`

## Explanation

For some reason, telegram decided to make every message in the album as a separate message, and i wasn't able to find any other repo that was able to handle albums, so i made my own.

If you want to know, how the principle works, you can either read ./src/main.rs, or this explanation:

1) Again, for some reason, teloxide decided to proccess updates from the same user sequentially, and it means that you can't just async wait to get new updates. I needed to make a custom dictribution function, to explicitly tell teloxide to proccess all messages in media groups at the same time (async)

2) Then, in the handler function, we get an arc mutex and add the recieved message for this user to it.

3) After that we wait for some latency period (100 ms works fine), and in each instance of handler we check, if the length before and after the latency period is the same. If it is, it means that this was the last message in the media group, as no more changes to the mutex were made, and we can safely assume that all the messages of this media group were collected. 

4) We clean the mutex for this user to make it ready for the next media group, and process all the messages. In this example, it just prints, how many messages are in this media group.
