---
layout: post
title: SwiftUI之CoreData
date: 2021-07-20
tags: SwiftUI
---

#### 1.CoreData是对sqlit的封装(面向对象变成)

#### 2.因为系统原生支持，可以和 Xcode 完美的结合

#### 3.需要手动创建xcdatamodeld文件，文件名称一般命名工程名称

#### 4.一般选择让 `Core Data` 自动生成与模型相匹配的代码(不用单独再写model类/结构体)

![demo]({{ "/assets/img/xcdatamodeld.jpeg" | absolute_url }})


注意:西瓜视频和今日头条原先强依赖 Core Data，但因为「某些性能」问题，均已全部撤出。

### 1.构建持久化对象
```swift
import CoreData

struct PersistenceController {
    // MARK: - 1. PERSISTENT CONTROLLER
    static let shared = PersistenceController()
    
    // MARK: - 2. PERSISTENT CONTAINER
    let container: NSPersistentContainer
    
    // MARK: - 3. INITIALIZATION (load rhe persistent store)
    init(inMemory: Bool = false) {
        container = NSPersistentContainer(name: "Devote_SwiftUI")
        if inMemory {
            container.persistentStoreDescriptions.first!.url = URL(fileURLWithPath: "/dev/null")
        }
        container.loadPersistentStores(completionHandler: { (storeDescription, error) in
            if let error = error as NSError? {
                fatalError("Unresolved error \(error), \(error.userInfo)")
            }
        })
        container.viewContext.automaticallyMergesChangesFromParent = true
    }
}
```

### 2.在App启动时候配置全局上下文
```swift
@main
struct Devote_SwiftUIApp: App {
    let persistenceController = PersistenceController.shared
    @AppStorage("isDarkMode") var isDarkMode: Bool = false

    var body: some Scene {
        WindowGroup {
            ContentView()
                .environment(\.managedObjectContext, persistenceController.container.viewContext)
                .preferredColorScheme(isDarkMode ? .dark : .light)
            // managed Object Contect in the Enviornment
        }
    }
}
```
### 3.增删改查
```swift
// 获取上下文变量
@Environment(\.managedObjectContext) private var viewContext

// 1.查(获取数据)
@FetchRequest(
    sortDescriptors: [NSSortDescriptor(keyPath: \Item.timestamp, ascending: true)],
    animation: .default)
private var items: FetchedResults<Item>

// 2.增(添加数据)
private func addItem() {
    withAnimation {
        let newItem = Item(context: viewContext)
        newItem.timestamp = Date()
        newItem.task = task
        newItem.completion = false
        newItem.id = UUID()

        do {
            try viewContext.save()
        } catch {
            let nsError = error as NSError
            fatalError("Unresolved error \(nsError), \(nsError.userInfo)")
        }
        
        task = ""
        hideKeyboard()
        isShowing = false
    }
}

// 3.删(删除数据)
private func deleteItems(offsets: IndexSet) {
    withAnimation {
        offsets.map { items[$0] }.forEach(viewContext.delete)

        do {
            try viewContext.save()
        } catch {
            let nsError = error as NSError
            fatalError("Unresolved error \(nsError), \(nsError.userInfo)")
        }
    }
}
```

参考博客: https://www.jianshu.com/p/00178c04a43d

参考项目: https://github.com/dongxiexidu/SwiftUI_Udemy 中Devote_SwiftUI项目

