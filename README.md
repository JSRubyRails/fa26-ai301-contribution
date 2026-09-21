# fa26-ai301-contribution


# Contribution [#]: agent-tools-mcp-hub

**Contribution Number:** 1 

**Student:** Elias Zegeye 

**Issue:** https://github.com/tarunjandra/agent-tools-mcp-hub/issues/101 

**Status:** Phase III Complete

---

## Why I Chose This Issue

[1-2 paragraphs explaining why this issue interests you, how it matches your skills/learning goals, what you hope to learn]

I chose issue #101 "Add Fear and Greed Crypto Sentiment Index Tool (Python)" because it provides an opportunity to strengthen my Python skills while working on a project that closely aligns with my interests in both cryptocurrency and investing as a whole. I find this especially interesting because market sentiment is an important factor in understanding how investors react to changing market conditions, particularly in the crypto market.

This issue involves creating a Python tool that retrieves the Crypto Fear and Greed Index, including the current sentiment score, classification, and historical trends. Working on this issue would allow me to gain practical experience with Python, APIs, data handling, and integrating a new tool into an existing AI-agent project. By implementing the tool and ensuring it meets the project's validation requirements, I would not only develop my own technical skills but also add a useful capability that could help users and AI agents better analyze market sentiment when making investment-related decisions.

---

## Understanding the Issue

### Problem Description

The agent-tools-mcp-hub repository provides a collection of standalone tools that AI agents can call to retrieve external data. Currently, the hub has no tool for retrieving crypto market sentiment. Agents can access price and market data, but there is no way to answer questions about how investors feel about the market — whether sentiment is in Extreme Fear, Greed, or somewhere in between. 

What's missing is a new tool directory, tools/crypto_fear_greed_index/, containing the four files the hub requires (tool.py, metadata.json, requirements.txt, and README.md), which exposes a days parameter and returns the current sentiment score, its classification label, and a multi-day trend history in a structured format that conforms to the repository's tool schema.


### Expected Behavior

After this tool is added, an agent or user should be able to call crypto_fear_greed_index with an optional days parameter (integer, default 7, maximum 30) and receive a structured response containing three things: the current Fear and Greed score as an integer from 0 to 100, the corresponding classification label (Extreme Fear, Fear, Neutral, Greed, or Extreme Greed), and a day-by-day history covering the requested range so that the direction of sentiment over time is visible, not just the latest reading.

### Current Behavior

The tools/crypto_fear_greed_index/ directory does not exist. There is no tool in the hub that returns crypto sentiment data, so a request for the Fear and Greed Index has no tool to resolve to — the agent either has no answer or must fall back on data outside the repository.

### Affected Components

tools/crypto_fear_greed_index would be a new directory which contains the following:
- tool.py, which is the implementation
- metadata.json, which is the tool's name and description 
- requirements.txt, which lists the python dependencies (if any)
- README.md, which is usage and example output of the directory 

scripts/validate_tools.py would also define the contract the new files must satisfy 

---

## Reproduction Process

### Environment Setup

Forked and cloned the repository agent-tools-mcp-hub, then created a working branch through these steps:
- git clone https://github.com/tarunjandra/agent-tools-mcp-hub.git
- cd agent-tools-mcp-hub
- git checkout -b feat/add-<fix-issue-crypto-fear-greed>

Working branch: https://github.com/shanker-codepath/agent-tools-mcp-hub/tree/fear-and-greed 

### Steps to Reproduce

1. Go to the /tools directory 
2. Find any directories that relates to the crypto fear and greed index 
3. It's expected to find a directory that fetches the daily Crypto Fear and Greed Index score and historical sentiment ratings (Extreme Fear, Fear, Neutral, Greed, Extreme Greed) from the public Alternative.me API. Currently, there is no directory that contains this. 

### Reproduction Evidence

- **Commit showing reproduction:** https://github.com/shanker-codepath/agent-tools-mcp-hub/tree/fear-and-greed 

- **Screenshots/logs:** Output of python scripts/validate_tools.py run 
- **My findings:** Confirmed the Fear and Greed Index API is public and it requires no API key. It also confirms it supports a parameter that returns the current sentiment score, classification label, and multi-day trend history

---

## Solution Approach

### Analysis

Because this is a new feature rather than a defect, the analysis is less about a root cause and more about the shape of the data source and the constraints it places on the tool's design. The Alternative.me Fear and Greed endpoint (https://api.alternative.me/fng/) is public and requires no API key or authentication, which keeps the tool's dependency surface small. It accepts a limit parameter controlling how many daily data points are returned, which maps directly onto the issue's days parameter. 

### Proposed Solution

Add a self-contained tool at tools/crypto_fear_greed_index/ that wraps the Alternative.me endpoint behind the hub's standard tool interface. tool.py will accept a days argument, validate it as an integer between 1 and 30 (defaulting to 7), request that many data points from the API, and return a structured result containing the current score, the current classification label, and a list of prior readings with their dates, scores, and labels. 

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** The hub has no crypto sentiment tool, and this adds one that returns score, label, and multi-day trend from a public API.

**Match:** It matches the setup in other tools by having tool.py, metadata.json, README.md, and requirements.txt, and can call a public REST API with query parameters

**Plan:** 
1. Read scripts/validate_tools.py to determine the exact required structure and metadata fields.
2. Examine an existing tool directory to match naming, structure, and error-handling conventions.
3. Create tools/crypto_fear_greed_index/ and scaffold the four required files.
4. Implement the API request and response parsing in tool.py, converting string scores to integers and Unix timestamps to readable dates.
5. Add parameter validation for days (integer, default 7, clamped or rejected above 30).
6. Add error handling for network failures and unexpected responses.
7. Write metadata.json declaring the tool and its parameter schema, and write README.md with usage and sample output.
8. Run python3 scripts/validate_tools.py and fix any validation errors.
9. Manually test with several days values including boundaries (1, 7, 30, 31, 0, non-integer). 


**Implement:** https://github.com/shanker-codepath/agent-tools-mcp-hub/tree/fear-and-greed

**Review:** Will review the "Tool Quality Guidelines" and "Types of Contributions Welcome" to make sure the tool meets all the requirements

**Evaluate:** validate_tools.py exits clean, manual calls at the boundary values return correct shapes; the score and label match what the Alternative.me site shows for the same day; the error path returns a message instead of a traceback when the network is unavailable.

---

## Testing Strategy

### Unit Tests

- No tests were in this repo, so none were added to the PR

### Integration Tests

N/A

### Manual Testing

The fear and greed crypto tool was manually tested by using an existing tool as a reference point

---

## Implementation Notes

### Week [2] Progress

This week was spent on discovery and planning rather than writing code. I started by reading through the repository to understand the problem the issue was describing: the hub gives AI agents access to crypto price and market data, but nothing that answers how investors feel about the market. To confirm that gap was real and not just undocumented, I went through the `tools/` directory and checked the existing tool directories for anything sentiment-related. There was no crypto sentiment tool anywhere in the hub, which confirmed the issue was still valid and worth building, and I used that to sketch a plan for the tool I wanted to build.


### Week [3] Progress

Building on the plan from Week 2, this week I implemented the tool and added the `tools/crypto_fear_and_greed_index/` directory, which contains:

- **`tool.py`** — the implementation. It exposes `get_crypto_fear_and_greed_index(days=7)`, validates `days` as an integer between 1 and 30, requests that many daily data points from the Alternative.me endpoint using the `limit` parameter identified in Week 2, and returns a structured summary of the requested period: the `high` and `low` sentiment scores, the `average` score across those days, and the `most_common_rating` (one of Extreme Fear, Fear, Neutral, Greed, Extreme Greed). It also handles network failures and unexpected responses so a caller gets a message back rather than a traceback.
- **`metadata.json`** — the tool's name, description, and parameter schema, declaring `days` as an optional integer defaulting to 7.
- **`registry.json`** — registers the tool so the hub picks it up alongside the existing tools.
- **`README.md`** — a parameter table for `days` and a usage example showing how to call the tool and read the returned fields.

---

### Code Changes

- **Files modified:** Added a new folder called "tools/crypto_fear_and_greed_index/" which contained tool.py, metadata.json, registry.json, and a README.md

- **Key commits:** https://github.com/shanker-codepath/agent-tools-mcp-hub/tree/fear-and-greed 

- **Approach decisions:** Rather than designing the tool's layout from scratch, I built it by copying the format of an existing directory under `tools/` and following its conventions for file structure, naming, metadata fields, and error handling. Matching a tool the maintainers had already accepted meant the new directory would satisfy the repository's validation requirements and read consistently with the rest of the hub, instead of introducing a second style a reviewer would have to reconcile. Within that structure, the tool exposes a single optional `days` parameter (integer, 1–30, default 7) that maps directly onto the API's `limit` parameter. To check for potential issues, I ran python scripts/validatE_tools.py throughout development.

---

## Pull Request

**PR Link:** https://github.com/shanker-codepath/agent-tools-mcp-hub/tree/fear-and-greed


**PR Description:**

The hub has tools for crypto price and market data but nothing for market sentiment, so there was no way for an agent to answer whether the market is in Extreme Fear, Greed, or somewhere in between. This PR adds `tools/crypto_fear_and_greed_index/` (tool.py, metadata.json, registry.json, README.md) wrapping the public Alternative.me Fear & Greed endpoint. It requires no API key, so the tool is zero-configuration, and its `limit` parameter maps directly onto the `days` parameter the issue asked for. Calling `get_crypto_fear_and_greed_index(days=7)` returns the `high`, `low`, and `average` sentiment scores over the requested window plus the `most_common_rating` (Extreme Fear, Fear, Neutral, Greed, or Extreme Greed); `days` is an optional integer from 1 to 30, defaulting to 7.

I based the directory layout, metadata fields, and error handling on an existing tool under `tools/` so this stays consistent with the rest of the hub, and network or unexpected-response failures return a message rather than a traceback. 


**Maintainer Feedback:** N/A


**Status:** Solved

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
