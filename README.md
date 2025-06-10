# Gradio Agents &amp; MCP Hackathon 2025

**Problem**: I love RSS feeds, but need help keeping up with all of the content from my subscriptions.

**Solution**: Build a tool to allow LLMs to find and interact with RSS feeds on behalf of the user.
## Introduction

I love RSS feeds - they remind me of a time when the internet was a weird and wonderful place, filled with interesting content hiding behind every link. The tools to produce and navigate that content have improved by leaps and bounds. However,  the improvement has not come without some losses. Content often feels homogeneous and it is too often painfully apparent that your favorite platform has a large degree of control over what content you see and what content you don't.

This tool give the user back some of that control. It let's them decide what content and sources they are interested in. I built it because I want access to diverse, unfiltered publishing by many sources, paired with modern AI to help me navigate it. I want the model to help me ingest my feed, not create it for me!

My hackathon build is a pair of HuggingFace Spaces:

1. [![Server HuggingFace Space](https://github.com/gperdrizet/rss-mcp-server/actions/workflows/publish_hf_space.yml/badge.svg)](https://huggingface.co/spaces/Agents-MCP-Hackathon/rss-mcp-server) A MCP server exposing a tools that agents can use to interact with RSS feed content
2. [![Client HuggingFace Space](https://github.com/gperdrizet/rss-mcp-client/actions/workflows/publish_hf_space.yml/badge.svg)](https://huggingface.co/spaces/Agents-MCP-Hackathon/rss-mcp-client) A MCP client demonstration using the Anthropic API to to interact with the RSS MCP server.

Both were built on Gradio and run in HuggingFace Spaces.

![Engineering diagram](https://github.com/gperdrizet/MCP-hackathon/blob/main/assets/engineering_diagram.jpg "engineering diagram")

## The RSS MCP server

The RSS MCP server exposes a set of custom tools for interacting with RSS feeds - the calling agent can pass in a website name, URL or direct link to an RSS feed and get back information about the posts in that feed.

1. A feed for the website is found if it exists, using a combination of [findfeed](https://pypi.org/project/findfeed) and/or Google search with [googlesearch-python](https://pypi.org/project/googlesearch-python/) as needed.
2. Recent posts from the feed are extracted using [feedparser](https://pypi.org/project/feedparser).
3. Article content is either taken from the feed if avalible, or pulled from the linked article using [boilerpy3](https://pypi.org/project/boilerpy3/) and good old RegEx, if possible.
4. The article content is summarized using [Meta-Llama-3.1-8B-Instruct](https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct) on [Modal](https://modal.com).
5. The article title, link and summary are returned to the calling LLM

Phew!

## The RSS MCP client

The demonstration client is a simple agentic chatbot using [Anthropic's Claude Haiku 3](https://docs.anthropic.com/en/docs/about-claude/models/overview). It connects to the MCP server via a custom interface heavily inspired by Adel Zaalouk's excellent repo: [MCP SSE Client Python](https://github.com/zanetworker/mcp-sse-client-python).
