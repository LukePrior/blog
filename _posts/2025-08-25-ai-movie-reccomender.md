---
title: "AI Movie Recommender"
subtitle: "Building a Movie Recommender That Actually Works"
date: 2025-08-25 00:00:00 -0400
img_path: /assets/img/
categories: [AI]
---

> Turns out transformers aren't just good at predicting the next token in a sentence—they're surprisingly good at predicting the next film in your viewing history too. Here's how I built a movie recommender in a weekend that actually works, using nothing but watch history and a simplified version of Netflix's latest ML approach.
{: .prompt-info }


I'm a bit of a movie nerd with nearly 1000 films logged on Letterboxd, but I've got the classic problem: I never rate anything, and I never know what to watch next. Most recommendation systems rely heavily on your ratings as a primary signal, which left me completely out in the cold. Netflix wants your thumbs up/down, IMDb wants your stars, and even Letterboxd's own recommendations work better when you rate everything.

This frustration coincided with my recent deep dive into AI fundamentals. After reading Netflix's paper[^netflix-paper] on their transformer-based recommendation foundation model, I had a lightbulb moment: what if I could build a recommender that works purely from watch history, treating movie consumption like a sequence prediction problem?

## Why Sequential Recommendation?

Traditional collaborative filtering approaches are everywhere "users who liked X also liked Y" but they're fundamentally limited by the rating bottleneck. Netflix's paper introduced a different paradigm: treat your viewing history as a sequence, like sentences in language, and use transformers to predict what comes next.

The insight is brilliant in its simplicity. Your movie preferences aren't just a static list of likes and dislikes; they're a temporal journey. The person who watched Pulp Fiction then Kill Bill then Django Unchained is telling a story about their evolving taste for Tarantino. A transformer can learn these patterns without needing explicit ratings.

I decided to implement a simplified version of their SASRec (Self-Attentive Sequential Recommendation) approach, stripping away the complexity around cold-start problems and new content to focus on the core sequential learning.

## The Dataset Challenge

I used the MovieLens 32M dataset[^movielens-dataset], 32 million ratings across 87,000 movies with a 2023 cutoff. But raw ratings data is messy. Some users rate everything 4-5 stars, others are harsh critics averaging 2-3 stars. Simply filtering for "high ratings" would miss most of the harsh critics' actual favourites.

My solution: calculate each user's 70th percentile rating threshold individually. For the generous rater, this might be 4.5 stars. For the critic, it might be 3.0 stars. This personalised threshold lets me extract what each user genuinely enjoyed, regardless of their rating style.

I then filtered to users with at least 20 films above their threshold—enough data to detect meaningful patterns but not so restrictive as to lose too much training data. The final dataset: about 6 million positive viewing interactions across 30,000 films from roughly 80,000 users.

The preprocessing sorted each user's positive films chronologically, creating sequences like: `[The Matrix, Blade Runner, Minority Report, Ex Machina...]`. The model learns to predict the next film in these sequences.

## Training the Model

The architecture is a relatively simple transformer: 128-dimensional embeddings, 8 attention heads, 2 layers, with a maximum sequence length of 50 films. Each movie gets embedded into a high-dimensional space where the model learns sequential viewing patterns from the training data. If users frequently watch certain types of films in sequence, the model can learn to predict these transitions, but without any explicit understanding of genres or themes.

Training took about 2 hours on my GTX 1070 across 6 epochs. I used an 80/20 train/validation split and tracked top-5 accuracy instead of exact-match accuracy. Why top-5? Because someone who watched The Godfather might reasonably watch Goodfellas, Scarface, Casino, or The Departed next. All are valid continuations of their crime film journey.

On paper, the model achieved a 10% top-5 accuracy. In the vast landscape of 30,000 films, correctly predicting the user's very next choice in the top five is a strong signal. As testing revealed, this level of accuracy was more than enough to generate uncannily relevant and personalized recommendations.

## Making It Web-Ready

Getting the model running in a browser was unexpectedly straightforward. PyTorch's native ONNX export converted the trained model to a 30MB file that runs entirely client-side using ONNX.js. No server needed—just load the model and start recommending.

The data flow is elegant: input a sequence of movie IDs (left-padded to 50 slots), get back probability scores for all 30,000 movies, sort by confidence, and return the top 10. The whole inference happens in milliseconds.

For the Letterboxd integration, I built a quick scraper that fetches your viewing history and fuzzy-matches titles against the MovieLens database. It's not robust—names like "The Lord of the Rings: The Fellowship of the Ring" vs "Lord of the Rings, The: The Fellowship of the Ring" require careful normalisation—but it works well enough for the prototype.

## What Actually Works

Testing on myself first (always dangerous for recommendation systems), it suggested films I'd already watched and loved, which was encouraging self-validation. More importantly, when I tested with film-obsessed friends, it consistently delivered surprisingly relevant recommendations.

A friend who's drawn to gritty psychological thrillers got recommendations that were genuinely perfect—films like Rosemary's Baby, Suspiria, and The King of Comedy that matched their taste for unsettling, raw tension. Another friend who gravitates towards feel-good cinema got a brilliant mix of crowd-pleasers like The Princess Bride, Back to the Future, and Stardust that all shared that warm, uplifting charm. The model seemed to understand not just genre preferences, but stylistic and thematic preferences.

There's a slight bias toward popular films (they appear more frequently in training data), but it's not overwhelming. The model balances popularity with personalisation reasonably well.

One major advantage over platform-specific recommendations: you see suggestions from the entire film landscape. Netflix only recommends what Netflix has. This system draws from decades of cinema history without licensing constraints.

## The Development Experience

Nearly all the code was generated by Claude Sonnet 4. I'd describe the problem, share error messages, and get working solutions. The transformer implementation, training loop, ONNX conversion, and even the web interface came together with minimal manual coding from me.

This felt like a glimpse of a different kind of development workflow—less about wrestling with syntax and more about architecting solutions and iterating on ideas. The total time investment was about 10 hours from initial concept to working prototype.

The biggest technical surprise was how straightforward modern ML deployment has become. Converting PyTorch to ONNX to browser-runnable JavaScript felt almost too easy. The data types are simple (arrays of integers), the inference is fast, and the user experience is immediate.

## What I Learned

This project reinforced how much the field has democratised. A few years ago, building a recommendation system would have required serious infrastructure, expertise in distributed systems, and complex deployment pipelines. Now, with consumer GPUs, pre-trained model architectures, and client-side inference, it's genuinely accessible to weekend hobbyists.

The sequential approach to recommendations feels more intuitive than traditional collaborative filtering. Your viewing history tells a story, and transformers are excellent at understanding stories. The temporal aspect captures how tastes evolve, seasonal viewing patterns, and contextual preferences in ways that static rating matrices miss.

That said, the system has clear limitations. It only knows films through 2023, the Letterboxd scraping is fragile, and the frontend is admittedly janky. But as a proof of concept for what's possible with modern tools and a few hours of focused work, it exceeded my expectations.

## Try It Yourself

Try it out and see what it recommends based on your watch history. You can either enter a Letterboxd username to automatically fetch your films, or manually input movie titles. Fair warning: the interface is rough around the edges, but the recommendations are surprisingly solid.

You can try the model [here](https://lukeprior.github.io/LetterboxdReccomender/) and the whole project is [open source](https://github.com/LukePrior/LetterboxdReccomender) if you want to dig into the implementation, train your own version, or build something better.

Not bad for a weekend project that actually solves a real problem. Now I just need to find time to watch all these recommendations.

## References

[^netflix-paper]: Netflix Tech Blog: Foundation Model for Personalized Recommendation. <https://netflixtechblog.com/foundation-model-for-personalized-recommendation-1a0bd8e02d39>
[^movielens-dataset]: MovieLens 32M dataset. <https://grouplens.org/datasets/movielens/32m/>
