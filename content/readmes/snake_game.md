---
title: "Snake Game"
date: 2024-09-18T23:36:36-06:00
draft: false
author: Robert Viquez
tags: 
    - Python
    - Turtle Graphics
    - Game
image: https://res.cloudinary.com/dqyllgamr/image/upload/v1726710789/snake4_flrjwi
# video:
description: Classic Snake Game in Python 
toc: true
---

A simple implementation of the classic Snake Game using the Turtle module from Python. The game features a snake that grows as it eats food, and the player must avoid colliding with the walls or the snake itself. Inspired by the classic arcade game.

## Features

- Simple gameplay
- Basic scoring system

## Installation

<ol>
    <li><strong>Download the Project Files</strong>
        <ul>
            <li>Download the ZIP file of the project from <a href="https://github.com/rvqzs/snake_game/archive/refs/heads/master.zip">this link</a>.</li>
        </ul>
    </li>
    <li><strong>Extract the ZIP File</strong>
        <ul>
            <li>After downloading, extract the ZIP file to a directory of your choice. This will create a folder named <code>snake_game-master</code> containing the project files.</li>
        </ul>
    </li>
    <li><strong>Check Python Installation</strong>
        <ul>
            <li>To check if Python is installed on your system, open a terminal or command prompt and run:
                <pre><code>python --version</code></pre>
            </li>
            <li>If Python is not installed, download and install Python from <a href="https://www.python.org/downloads/">python.org</a>. Ensure you download Python version 3.11.8 or above.</li>
        </ul>
    </li>
    <li><strong>Install Turtle Module</strong>
        <ul>
            <li>The Turtle module is included with most Python distributions. If it's not installed, you can install it via pip:
                <pre><code>pip install PythonTurtle</code></pre>
            </li>
        </ul>
    </li>
</ol>

## Usage

<ol>
    <li><strong>Open Terminal or PowerShell</strong>
        <ul>
            <li>On Windows, you can open Command Prompt or PowerShell by pressing <code>Win + R</code>, typing <code>cmd</code> or <code>powershell</code>, and pressing <code>Enter</code>.</li>
        </ul>
    </li>
    <li><strong>Navigate to the Project Directory</strong>
        <ul>
            <li>Use the <code>cd</code> command to change to the directory where you extracted the ZIP file. For example, if you extracted the files to <code>C:\Users\username\Downloads\snake_game-master</code>, you would run:
                <pre><code>cd C:\Users\username\Downloads\snake_game-master</code></pre>
            </li>
        </ul>
    </li>
    <li><strong>Run the Game</strong>
        <ul>
            <li>Start the game by running the following command:
                <pre><code>python main.py</code></pre>
            </li>
            <li>If you encounter issues, try:
                <pre><code>py main.py</code></pre>
            </li>
        </ul>
    </li>
    <li><strong>Start Playing</strong>
        <ul>
            <li>Use the arrow keys to control the direction of the snake.</li>
            <li>Eat the food (represented by a circle) to grow the snake and increase your score.</li>
            <li>Avoid colliding with the walls or the snake's own body.</li>
            <li>The game ends when the snake collides with itself or the boundaries.</li>
        </ul>
    </li>
</ol>


---