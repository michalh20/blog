# Star Trek Online

In July 2023, I came across a video from [DEF CON 25](https://www.youtube.com/watch?v=ZAUf_ygqsDo&t). where the presenter, called Manfred describes his journey into game hacking over the past 20 years. The video fascinated me so much that I decided I wanted to give it a try as well. However, I didn’t have the necessary skills or knowledge—apart from a few hours spent experimenting with Cheat Engine, some basic knowledge of Python, and a little understanding of C++ syntax. I downloaded x64dbg and Ghidra and began experimenting without any guides.

For my experiments, I chose an older game, Star Trek Online, which didn’t have anti-cheat measures (which was very helpful). Back in 2014, I had watched a video by a person named Skybuck Flying, who had created a simple but effective bot to generate in-game currency. It was a bot that used image recognition and clicked buttons in the game window. However, this limited the ability to use your computer, as the bot would take control of the mouse, and you could only watch.

I found this approach to be quite amateurish and difficult to scale. After about four months, I managed to program my first bot prototype. Instead of using the mouse/keyboard and image recognition, my bot directly called functions from the game’s process. I could also close the game window and still have up to 40 instances of the game running at once, without needing expensive hardware (a $20 Windows VPS was all it took).

Once I had achieved my initial goal, I decided to look for bugs and exploits in the game. I found several, but perhaps the most significant was a 1-click remote code execution exploit on the victim’s computer. All it took was convincing the victim to click a button in the game to visit the guild website. The only condition was that the victim needed to have Python or Java installed. Files like .py or .jar don’t have the "Mark of the Web" assigned to them when downloaded from the internet. (If you bypass the Mark of the Web, you can even run an .exe file, though such an exploit could be sold for a lot of money so nobody will use it for hacking players of a small MMORPG.)

## How does it work ?

Game is using WinAPI Function ShellExecuteW to open URLs 

```c
HINSTANCE ShellExecuteW(
  [in, optional] HWND    hwnd,
  [in, optional] LPCWSTR lpOperation,
  [in]           LPCWSTR lpFile,
  [in, optional] LPCWSTR lpParameters,
  [in, optional] LPCWSTR lpDirectory,
  [in]           INT     nShowCmd
);
end
```
The value of the lpOperation parameter is set to L"open", which allows us, for example, to open the command line when clicking on the "Visit website" button if we set the guild's URL as "cmd.exe" as the guild master. This becomes interesting when we host our own WebDAV server, upload our custom Python/Java code to it, and then set the guild website URL to something like "file://webdavurl/code.py".
Using URI scheme "file://" is important as it is being used by Windows for WebDAV servers and when ShellExecuteW is called with lpOperation param L"open" and lpFile set as URL of our WebDAV server Windows will open and run code.py automatically, this can lead to remote code execution.

<video src='https://github.com/michalh20/blog/blob/main/STOPoC.mkv' width=180/>
