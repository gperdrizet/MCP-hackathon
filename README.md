# Gradio Agents &amp; MCP Hackathon 2025

**Problem**: I love RSS feeds, but want help keeping up with the content flux.

**Solution**: Build tools for LLMs to find and interact with RSS feeds on my behalf.

## Introduction

I love RSS feeds - they remind me of a time when the internet was a weird and wonderful place, filled with interesting content behind every link. The tools to produce and navigate content have improved by leaps and bounds. However, improvement has not come without cost. Content often feels homogeneous and highly curated. The degree to which your favorite platform controls what you see and what you don't is painfully apparent.

This tool gives the user back some of that control. It lets you decide what content sources you are interested in, and then pull from them directly. I built it because I want access to diverse and unfiltered publishing by many sources, and I want modern AI to help me navigate it. I want the model to help me ingest my feed, not create it for me!

## The build

My hackathon build is a pair of HuggingFace Spaces:

1. [![Server HuggingFace Space](https://github.com/gperdrizet/rss-mcp-server/actions/workflows/publish_hf_space.yml/badge.svg)](https://huggingface.co/spaces/Agents-MCP-Hackathon/rss-mcp-server) [A MCP server](https://huggingface.co/spaces/Agents-MCP-Hackathon/rss-mcp-server) exposing a tools that agents can use to find and interact with RSS feed content.
2. [![Client HuggingFace Space](https://github.com/gperdrizet/rss-mcp-client/actions/workflows/publish_hf_space.yml/badge.svg)](https://huggingface.co/spaces/Agents-MCP-Hackathon/rss-mcp-client) [A MCP client](https://huggingface.co/spaces/Agents-MCP-Hackathon/rss-mcp-client) demonstrating a agentic chatbot using the Anthropic API to to interact with the RSS MCP server.

Both were built with Gradio and run in HuggingFace Spaces.

![Engineering diagram](https://github.com/gperdrizet/MCP-hackathon/blob/main/assets/engineering_diagram.jpg "engineering diagram")

## The RSS MCP server

The RSS MCP server exposes a set of custom tools for interacting with RSS feeds - the calling agent can pass in a website name, URL or direct link to an RSS feed and get back links, summaries and full-text content from that feed.

### Features

1. Feeds are found using a combination of [findfeed](https://pypi.org/project/findfeed) and/or Google search with [googlesearch-python](https://pypi.org/project/googlesearch-python/) as needed.
2. Recent posts from the feed are extracted using [feedparser](https://pypi.org/project/feedparser).
3. Article content is either taken from the feed if avalible, or pulled from the linked article using [boilerpy3](https://pypi.org/project/boilerpy3/) and good old RegEx, if possible.        
4. The article content is summarized using [Meta-Llama-3.1-8B-Instruct](https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct) on [Modal](https://modal.com).
5. Article content is embedded into a vector database on [Upstash](https://upstash.com/) for RAG.
6. Results are cached to a Redis database on [Upstash](https://upstash.com/) to minimize unnecessary calls to the web crawler/scraper or summarizer.

### MCP tools

1. `get_feed()`: Given a website name or URL, find its RSS feed and
    return recent article titles, links and a generated summary of content if
    avalible. Caches results for fast retrieval by other tools. Embeds content
    to vector database for subsequent RAG.
2. `context_search()`: Vector search on article content for RAG context.
3. `find_article()`: Uses vector search on article content to find title of article
    that user is referring to.
4. `get_summary()`: Gets article summary from Redis cache using article title.
5. `get_link()`: Gets article link from Redis cache using article title.

Phew!

## The RSS MCP client

The demonstration client is an agentic chatbot using [Anthropic's Claude Haiku 3.5](https://docs.anthropic.com/en/docs/about-claude/models/overview). It connects to the MCP server via a custom interface heavily inspired by Adel Zaalouk's excellent repo: [MCP SSE Client Python](https://github.com/zanetworker/mcp-sse-client-python).

### Features

1. Inference with Anthropic's fast and cost effective claude-3.5-haiku model.
2. Custom MCP client with asynchronous server side events, retry and error handling based on the excellent repo by [Adel Zaalouk](https://github.com/zanetworker/mcp-playground/tree/main).
3. Multi-turn re-prompting to allow LLM workflows with multiple tool calls.
4. Queue and worker system to show user what's going on 'under the hood' while the model calls tools and generates replies. 
    
