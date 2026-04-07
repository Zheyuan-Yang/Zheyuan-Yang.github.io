---
title: MemoLens
date: 2026-03-28
categories: projects
---

Github link: https://github.com/Zheyuan-Yang/MemoLens

## Award
The second place of Photon Track in USC Build with Trae and MiniMax Hackathon 2026.

## System Diagram
![](/images/memolens.png)

## About this project

### What It Is
MemoLens is a local-first photo retrieval tool for turning a large personal image library into a tighter, post-ready selection.

The project currently includes:

- an Electron desktop app for indexing, prompt-based retrieval, curation, and export
- a Flask backend that serves retrieval, indexing, and file access APIs
- a local SQLite photo index backed by image analysis metadata
- a Discord bot that can send retrieval results back into chat

### What It Can Do
- index a local image folder into a photo_index.db
- pause and resume local indexing in the desktop app
- retrieve images from natural-language prompts
- generate a curated 9-image draft with title and caption
- export the resulting draft as a text file
- send retrieval results through Discord with resized image attachments
