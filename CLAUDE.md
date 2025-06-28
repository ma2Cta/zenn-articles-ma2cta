# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

このリポジトリはZenn記事を構造化されたディレクトリアプローチで管理するために設計されています。

## ディレクトリ構造

- `articles/` - Zenn記事のMarkdownファイル
- `images/` - 記事で使用する画像
  - `common/` - 複数記事で共有する画像
  - 必要に応じて記事別フォルダ
- `templates/` - 一貫したフォーマットのための記事テンプレート
- `scripts/` - 記事管理と自動化のためのユーティリティスクリプト

## 重要な原則

- 記事のステータス管理はZennのWebインターフェースで行い、ディレクトリ構造には反映しない
- 画像は記事別に整理するか、再利用可能なアセットは`common/`に配置する
- テンプレートを使用して記事間の一貫性を保つ
- 整理が必要な場合は、各記事専用の画像フォルダを作成する