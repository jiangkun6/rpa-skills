---
name: ctrip-itinerary-workflow
description: Ctrip travel itinerary automation workflow. Use this skill when the user wants to automatically fill out travel plans on Ctrip (携程) with traveler count, destination, dining preferences, departure location, and dates.
---

# ctrip-itinerary-workflow

调用 DHF Agent **工作流**，自动填写携程旅游计划。

## 触发条件

当满足以下所有条件时，调用此工作流:
1. ✅ 已获取人数（成人/儿童/婴儿)
2. ✅ 已确认目的地城市
3. ✅ 已确认出发城市
4. ✅ 已确认出发日期
5. ✅ 已确认餐饮要求
6. ✅ 已生成 JSON 方案并验证通过
7. ✅ **调用工作流时订单号已填写**
