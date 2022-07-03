---
title: leetcode-535-encode-and-decode-tinyurl
tags:
  - leetcode
---

TinyURL is a URL shortening service where you enter a URL such as `https://leetcode.com/problems/design-tinyurl` and it returns a short URL such as `http://tinyurl.com/4e9iAk`.

Design the `encode` and `decode` methods for the TinyURL service. There is no restriction on how your encode/decode algorithm should work. You just need to ensure that a URL can be encoded to a tiny URL and the tiny URL can be decoded to the original URL.

解题思路：新建一个数据队列将数据的索引作为短url


```
class Solution {
    vector<string> map;
public:
    // Encodes a URL to a shortened URL.
    string encode(string longUrl) {
        map.push_back(longUrl);
        return "http://tinyurl.com/"+to_string(map.size()-1);
    }

    // Decodes a shortened URL to its original URL.
    string decode(string shortUrl) {
        auto pos = shortUrl.find_last_of("/");
        return map[stoi(shortUrl.substr(pos + 1))];
    }
};

// Your Solution object will be instantiated and called as such:
// Solution solution;
// solution.decode(solution.encode(url));
```
