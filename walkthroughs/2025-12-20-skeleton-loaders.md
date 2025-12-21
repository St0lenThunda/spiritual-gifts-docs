# Walkthrough: Skeleton Loaders for Loading states

## Problem
When fetching data for the Dashboard and Results pages, users were presented with a blank screen or a simple spinner, which led to a poor user experience and a perceived sense of slowness, especially during backend cold starts.

## Solution
Implemented skeleton loaders for both the Dashboard and Results pages. Skeleton loaders provide visual feedback that content is being loaded and maintain the layout's structure, reducing perceived latency.

## Changes

### [NEW] [SkeletonLoader.vue](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/components/common/SkeletonLoader.vue)
A generic, reusable skeleton component with a "shimmer" animation effect.

### [NEW] [DashboardSkeleton.vue](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/components/dashboard/DashboardSkeleton.vue)
A specific skeleton layout for the Dashboard page, including placeholders for stats cards and assessment history.

### [NEW] [ResultsSkeleton.vue](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/components/dashboard/ResultsSkeleton.vue)
A specific skeleton layout for the Results page, including placeholders for charts and summaries.

### [MODIFY] [Dashboard.vue](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/pages/Dashboard.vue)
Integrated `DashboardSkeleton` to show during data fetching.

### [MODIFY] [Results.vue](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/pages/Results.vue)
Integrated `ResultsSkeleton` to show during data fetching.

## Verification Results
- [x] Dashboard shows skeleton placeholders before data is loaded.
- [x] Results page shows chart skeletons before results are rendered.
- [x] Verified that skeleton loaders disappear once data fetching is complete.
