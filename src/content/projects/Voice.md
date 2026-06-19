---
title: "Voice Transform IME"
description: "面向 Android 输入法场景的上下文感知语音纠错项目"
date: "2026-06-19T18:48:06.852Z"
draft: false
showHeroImage: false
tags: []
categories: []
series: []
comments: true
sidebar:
  enable: false
  toc: true
  relatedPosts: false
---

# Voice Transform IME

## 项目简介

Voice Transform IME 是一个面向 Android 输入法场景的上下文感知语音纠错项目。它会在语音识别结果插入前，结合用户画像、专业词库、当前应用上下文、拼音/同音规则以及可选 LLM，对文本进行二次纠错。

这个项目不是独立聊天机器人，而是服务于真实输入法流程。例如当语音识别把“计组”等专业词误识别成相近读音的词时，系统可以根据用户背景和常用术语给出更合适的修正结果，并由用户确认后插入当前应用。

## 技术栈

- Android, Java/Kotlin, InputMethodService, SpeechRecognizer
- Trime/Rime 中文输入法集成
- Python, FastAPI, Pydantic, SQLite, Uvicorn
- 拼音/同音词规则纠错
- 可选 OpenAI-compatible LLM Relay

## 链接

- GitHub: https://github.com/Nek0mush1/voiceTransformForAndroid
