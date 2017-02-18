+++
comments = true
slug = "on-golang"
toc = false
date = "2016-10-16T02:26:49+05:30"
title = "on golang"
tags = ["golang"]
+++

# Hey go!

Been a while since I blogged here. When life gives you lemons... oh wait..

 ![shakthimaan](/files/shakthi.jpg)

  When life gives you dairy milk silk.. just eat it :P

# Golang : new dating

- Migrated org-mode blog to hugo.

### Task : Rename all .org files to .md

```.go
package main

import (
    "path/filepath"
    "os"
)

func main() {
    files, _ := filepath.Glob("*.org")
    for _, file := range files {
      basename := file[:len(file)-len(filepath.Ext(file))]
      os.Rename(basename+".org", basename+".md")
    }
}
```

```.shell
$ go run ~/rename.go
```

### Let's play with APIs

```.go
package main

import (
	"encoding/json"
	"fmt"
	"io/ioutil"
	"net/http"
)

type User struct {
	Id    string
	Karma int
	About string
}

type Item struct {
	Url   string
	Title string
	By    string
	Score int
}

type TopStories struct {
	Items []int
}

func getApiRaw(url string) []byte {
	resp, err := http.Get("https://hacker-news.firebaseio.com/v0" + url + ".json")
	if err != nil {
		fmt.Print(err)
		return []byte(nil)
	}
	defer resp.Body.Close()
	bodyBytes, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		fmt.Print(err)
		return []byte(nil)
	}
	return bodyBytes
}

func getItem(itemId int) Item {
	item := Item{}
	itemResp := getApiRaw("/item/" + fmt.Sprint(itemId))
	json.Unmarshal(itemResp, &item)
	return item
}

func getUser(userId string) User {
	user := User{}
	userResp := getApiRaw("/user/" + userId)
	json.Unmarshal(userResp, &user)
	return user
}

func getTopStories() TopStories {
	topStories := TopStories{}
	topStoriesResp := getApiRaw("/topstories")
	json.Unmarshal(topStoriesResp, &topStories.Items)
	return topStories
}

func main() {
	for _, itemId := range getTopStories().Items {
		item := getItem(itemId)
		fmt.Println("[" + fmt.Sprint(item.Score) + "] " + item.Title + " by " + item.By + " (" + fmt.Sprint(getUser(item.By).Karma) + ")")
	}
}
```
# How to do X in Go ?

- RTFM!
`$ godoc -http=:6060`
