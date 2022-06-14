```swift
import SwiftUI

struct ContentView: View {
    @StateObject var model = Model()
    
    class Model: ObservableObject {
        @Published var items = [
            Item(zone: "America", count: 589),
            Item(zone: "Asia", count: 67),
            Item(zone: "Africa", count: 207),
            Item(zone: "Oceania", count: 9)
        ]
        
        class Item: ObservableObject, Hashable {
            static func == (lhs: ContentView.Model.Item, rhs: ContentView.Model.Item) -> Bool {
                lhs.id == rhs.id
            }
            
            let id = UUID()
            let zone: String
            let count: Int
            
            @Published var selected: Bool = false
            
            init(zone: String, count: Int) {
                self.zone = zone
                self.count = count
            }
            
            func toggle() {
                selected.toggle()
            }
            
            func hash(into hasher: inout Hasher) {
                hasher.combine(id)
            }
        }
    }
    
    var body: some View {
        VStack {
            ForEach(Array(model.items.enumerated()), id: \.element) { index, item in
                HStack {
                    Text(item.zone)
                    Spacer()
                    Text(String(item.count))
                    Button {
                        item.toggle()
                        print(index, item.selected)
                    } label: {
                        Image(systemName: item.selected ? "checkmark.square" : "square")
                            .resizable()
                            .frame(width: 24, height: 24)
                            .font(.system(size: 20, weight: .regular, design: .default))
                    }
                }
                .padding(10)
                .onReceive(item.$selected) { isOn in
                    model.objectWillChange.send()
                }
            }
            
            Button {
                model.items.forEach { item in
                    item.selected = false
                }
                model.objectWillChange.send()
            } label: {
                Text("Clear")
            }

        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```
