# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Status

This repository is meant to hold multiple customized Next.js applications (`apps/<name>`) and shared packages (`packages/<name>`), but none have been scaffolded yet. There are no build, lint, or test commands. Update this file (or run `/init` again) once code is added.

## Repository conventions

- Git and editor config lives **only at the root**: `.gitignore`, `.gitattributes`, `.editorconfig`. Don't add nested `.gitignore` files or nested `.git` repos inside apps or packages. When scaffolding with `create-next-app`, pass `--disable-git` and delete the generated `.gitignore`.
- App-specific ignore rules go, path-scoped, in the "App-specific additions" section at the bottom of the root `.gitignore`.
- **LF-only line endings** for all text files (enforced by `.gitattributes`). Lockfiles are committed. All `.env` / `.env.*` files are ignored except templates (`.env.example`, etc.).
- See `README.md` for validation commands (`git check-ignore -v`, `git check-attr text eol --`, `git ls-files --eol`).
