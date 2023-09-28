---
title: "Pokemon TCG Dashboard"
categories:
  - blog
layout: single
classes: wide
tags:
  - Pokemon TCG
  - tournaments
  - PTCGL
  - dashboard
---

[![](https://img.shields.io/badge/GitHub-View%20on%20GitHub-blue?logo=github)](https://github.com/andrew-dang/limitless_scrape) [![](https://img.shields.io/badge/Heroku-View%20on%20Heroku-blue?logo=heroku)](https://limitlesstcg-analysis.herokuapp.com/)

## Motivation
The Pokemon Trading Card Game \(PTCG\) is a turn-based card game based on the ever popular game franchise. Each year, players compete in official events for prize money and aim to accumulate Championship Points which are required to receive an invite to play in the World Championships at the end of the year. The prize pool has increased for the 2024 season, and there is almost $5 million dollars up for grabs. The motivation for this project was to build a dashboard that tracks the performance of different decks over time to help players better understand different matchups so that they can select a deck that maximizes their chances of winning games at official events. 

## Data Source
The dashboard currently displays two graphs - a line graph showing win rates over time, and a match-up heatmap displaying overall win-rates between different decks. The data used to calculate these values are from online tournament results. [Limitless TCG](play.limitlesstcg.com) is a website that provides a platform for tournament organizers to host online tournaments with games being played on the official digital version of the card game, Pokemon Trading Card Game Live \(PTCGL\). 