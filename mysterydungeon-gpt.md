---
layout: post
title: "mysterydungeonGPT - An exploration in LLM text-to-map game level generation"
date: 2026-01-28
categories: machine-learning, game-development
permalink: /mysterydungeon-gpt/
---

## Introduction

Growing up, one of my favorite video games was the Pokémon Mystery Dungeon series. The idea of loading into a random map filled with valuable loot and powerful enemies, with no real idea of where you were going, was enthralling. There was always a real sense of accomplishment and satisfaction when a dungeon was successfully navigated with limited resources and the luck involved with every step of exploration.

I wanted to give players a way to not only play any number of maps they desired in a mystery dungeon environment, but also to allow them to request whatever they wished to be in the dungeon. Through the power of LLMs, players can now receive randomized maps that contain all the elements they desire in a seamless text-to-map flow.

Another major motivator for this work is to create a sandbox environment for developers to easily create a highly customizable map dataset for fine-tuning, as well as an equally customizable web game to test their creations. The end result: dungeons that can be played by everyone.

![Web Game]({{ '/blog_images/game_screenshot.png' | relative_url }})
## Building the Pipeline: Dataset Creation

The dataset was created procedurally using the algorithm provided in the SkyTemple repository. This algorithm forms the backbone of the map generation logic, and I customized it to generate simpler maps without elements like Kecleon shops, items, and monster rooms.

After map generation, spawn points for the player and stairs (exit) were added. The location of these spawn points is validated using a BFS (breadth-first search) algorithm, which ensures that the exit is reachable from the player spawn and that both spawns appear on walkable tiles. Once the dataset was generated, it was uploaded to HuggingFace for easy accessibility.

## Building the Pipeline: Training the Model

For this task, I chose to fine-tune **Qwen3-0.6B** due to its impressive performance-to-size ratio, which enabled consistently good results while using minimal resources. This model also supports switching between "thinking" and "non-thinking" modes, which proved crucial for the inference piece of the pipeline. Since the model outputs JSON as text, the additional "thinking" tokens had adverse effects on the map structure. This issue was remedied by enabling the model's "non-thinking" mode.

I used **LoRA (Low-Rank Adaptation)** to fine-tune the model. LoRA is a parameter-efficient fine-tuning method, as the trainable adapters are relatively small and memory-efficient. This attribute pairs nicely with the model's size, resulting in low training costs.

For the LoRA parameters, I increased both the `rank` and `lora_alpha` substantially to allow the model to capture the complexities of map generation. The other parameters were more straightforward; target modules, dropout, and bias settings followed standard baseline selections.

The training was run on **Modal**, which provided access to GPUs powerful enough to train the model and host the inference server. Training performance was monitored using **Weights & Biases**.

## The Coordinate-Based Solution

The first iterations of training and testing revealed a significant issue: a 70/30 class imbalance between wall and floor tiles. This wasn't something I had originally considered, and it resulted in the model outputting nothing but wall tiles. There was also a massive number of tokens being outputted to represent the full grid.

After trying solutions such as reducing the overall grid size and increasing the number of rooms in the training data, the solution that ultimately worked was transitioning to a **coordinate-based approach**. In this method, only the walkable tiles are stored, which results in approximately 90% fewer tokens and eliminates class imbalance, since the model is solely predicting walkable tiles.

Additionally, because the grid dimensions are passed in as metadata, grid reconstruction isn't affected—the location of walls is implicitly determined by the absence of walkable tiles. Finally, the smaller token sequences resulted in lower memory usage, allowing the use of a 2048 max context length instead of 6144+.

![Token Comparison]({{ '/blog_images/token_comparison.png' | relative_url }})

## Building the Pipeline: Inference

After training was completed and generation test results were deemed satisfactory, the final piece was deploying the model for inference. I again employed Modal for this task, and the server setup was completed using **SGLang**.

The key to the inference piece was adding the Qwen3-provided non-thinking Jinja template file, which enabled the inference output to return clean, usable JSON. This was the final piece of the pipeline. Once this was in place, I added logic to automatically save generated maps to the web game directory, allowing users to immediately play maps as they are generated.

## Results and Integration

For this project and due to limited GPU access, I limited the dataset to include only medium-difficulty dungeons containing 6 to 12 rooms, with a total dataset size of 5,000 maps for training and validation. Despite the limited dataset, the model performs very well in predicting valid, playable maps from the given prompts.

The training prompts used are shared below, along with some example maps that were generated.

![Sample Maps]({{ '/blog_images/example_maps.png' | relative_url }})
![Sample Prompts]({{ '/blog_images/example_prompts.png' | relative_url }})

## Lessons Learned and Future Work

The most valuable lessons mainly stemmed from managing expected results with respect to the limited compute I had available. Because Modal only supplies a small amount of free training credit, I had to streamline as much of the training and inference as possible through efficient fine-tuning methods and careful dataset curation.

This leads into my future work with this project: extending the complexity of the dataset to produce more populated and interesting maps. I believe that despite not having many parameters in the map metadata of my current dataset, it's a great starting point for anyone who wants to experiment and create their own game and its respective maps.

## Acknowledgements

- Built on [Qwen3-0.6B](https://huggingface.co/Qwen/Qwen3-0.6B)
- Uses [SGLang](https://github.com/sgl-project/sglang) for high-performance inference
- Deployed on [Modal](https://modal.com)
- Dataset hosted on [HuggingFace](https://huggingface.co/teamgas/mysterydungeondata)
- Dataset generation was built upon map code from [SkyTemple](https://github.com/SkyTemple/dungeon-eos)
- Special thanks to [Shyam Sudhakaran](https://github.com/shyamsn97) for guiding me through this project and helping navigate the many bugs along the way.
