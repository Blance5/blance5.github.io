---
layout: page
title: Baseball Game Scheduler
description: Invite-only app for organizing pickup baseball games with friends
img: assets/img/baseball-game-scheduler.png
importance: 0
category: fun
---

This app was built for a small group that regularly organizes pickup baseball games. It gives players a single place to schedule games, join available spots, see the current roster, and keep track of updates when a game fills up, changes, or gets cancelled.

The product is invite-only and designed primarily for mobile use. It supports full and half-player spots, live roster updates, push notifications, priority-based player replacement using the group's existing ranking rules, automatic game status transitions, and post-game cost sharing.

The following technologies were used in the making of this project:
- React 18 + Vite
- TypeScript
- Tailwind CSS
- Supabase Auth, Postgres, Realtime, and Edge Functions
- Web Push notifications with VAPID
- Vercel

Check out the project!:
- [Live App](https://bc-schedular.vercel.app/)
- [GitHub Repo](https://github.com/Blance5/BC_schedular)

The app is implemented as a progressive web app with a React frontend and a Supabase backend. Realtime updates keep the schedule and roster current, while authentication, edge functions, and scheduled jobs handle invitations, notifications, and the game lifecycle.
