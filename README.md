# Gradio Agents &amp; MCP Hackathon 2025

**Problem**: I love RSS feeds, but need help keeping up with all of the content from my subscriptions.

**Solution**: Build a tool to allow LLMs to find and interact with RSS feeds on behalf of the user.

My hackathon build is a pair of HuggingFace Spaces:

1. [![Server HuggingFace Space](https://github.com/gperdrizet/rss-mcp-server/actions/workflows/publish_hf_space.yml/badge.svg)](https://huggingface.co/spaces/Agents-MCP-Hackathon/rss-mcp-server) A MCP server exposing a tool that handles finding/parsing/summarizing RSS feeds
2. [![Client HuggingFace Space](https://github.com/gperdrizet/rss-mcp-client/actions/workflows/publish_hf_space.yml/badge.svg)](https://huggingface.co/spaces/Agents-MCP-Hackathon/rss-mcp-client) A MCP client using the Anthropic API to to interact with the RSS MCP server

Both were built on Gradio and run in HuggingFace Spaces.

![Engineering diagram](https://github.com/gperdrizet/MCP-hackathon/blob/main/assets/engineering_diagram.jpg "engineering diagram")

## The RSS MCP server

The RSS MCP server currently exposes one tool - the calling agent can pass in a website name, URL or direct link to an RSS feed and get back information about the posts in that feed.

1. A feed for the website is found if it exists, using a combination of [findfeed](https://pypi.org/project/findfeed) and/or Google search with [googlesearch-python](https://pypi.org/project/googlesearch-python/) as needed.
2. Recent posts from the feed are extracted using [feedparser](https://pypi.org/project/feedparser).
3. Article content is either taken from the feed if avalible, or pulled from the linked article using [boilerpy3](https://pypi.org/project/boilerpy3/) and good old RegEx, if possible.
4. The article content is summarized using [Meta-Llama-3.1-8B-Instruct](https://huggingface.co/meta-llama/Llama-3.1-8B-Instruct) on [Modal](https://modal.com).
5. The article title, link and summary are returned to the calling LLM

Phew!

## The RSS MCP client

The demonstration client is a simple agentic chatbot using [Anthropic's Claude Haiku 3](https://docs.anthropic.com/en/docs/about-claude/models/overview). It connects to the MCP server via a custom interface heavily inspired by Adel Zaalouk's excellent repo: [MCP SSE Client Python](https://github.com/zanetworker/mcp-sse-client-python).
