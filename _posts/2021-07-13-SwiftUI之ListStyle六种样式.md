---
layout: post
title: SwiftUI之ListStyle六种样式
date: 2021-07-13
tags: SwiftUI
---

List 本质是对TableView的封装

List默认样式.automatic

```swift
var body: some View {
    NavigationView {
        List {
            ForEach(menu) { section in
                Section(header: Text(section.name)) {
                    ForEach(section.items) { item in
                        NavigationLink(destination: ItemDetail(item: item)) {
                                ItemRow(item: item)
                        }
                    }
                }
            } // 这里listStyle设置无效
            .listRowSeparator(.hidden) // 隐藏分割线 iOS 15 新增的方法
            .listRowSeparatorTint(.green)// 修改分割线颜色
        }
        .navigationTitle("Menu")
        .listStyle(.automatic) // 默认, 有圆角,两边有15间距
//            .listStyle(.grouped) // 无圆角,两边无间距
//            .listStyle(.plain) // 无圆角,两边无间距,header会悬浮在顶部
//            .listStyle(.inset) // 无圆角,两边无间距,header会悬浮在顶部
//            .listStyle(.insetGrouped) // 有圆角,两边有15间距
//            .listStyle(.sidebar) // 有圆角,两边有15间距, 可折叠
//
    }
}
```

### 注意:listStyle设置在List{}外,设置在内部ForEach是无效的

**注意:** 隐藏分割线.listRowSeparator(.hidden) 需要在List内部

.automatic和.insetGrouped样式一样,有圆角,List两边都有约15的间距
![demo]({{ "/assets/img/listStyleAutomaticOrinsetGrouped.png" | absolute_url }})


.plain和.inset样式一样,无圆角,两边无间距,**header会悬浮在顶部**
![demo]({{ "/assets/img/listStyleInsetOrPlain.png" | absolute_url }})

.grouped样式
![demo]({{ "/assets/img/listStyleGrouped.png" | absolute_url }})

.sidebar样式,有圆角,两边有15间距, 可折叠,有折叠小箭头
![demo]({{ "/assets/img/listStyleSidebar.png" | absolute_url }})
