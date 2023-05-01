# Breaking Bad Quotes API w/ Async Await

Logan Koshenka - https://www.youtube.com/watch?v=Y-GxKhLRGyE

<img src="https://github.com/Brian-McIntosh/API-AsyncAwait/blob/main/API-AsyncAwait/images/1.png" width="250"/>

```swift
struct Quote: Codable {
    //var id = UUID()
    var quote: String
    var author: String
}

class ContentViewModel: ObservableObject {
    @Published var quotes: [Quote] = []
    
    let dummyData: [Quote] = [
        Quote(quote: "Give me my baby!", author: "Skylar"),
        Quote(quote: "I am the danger.", author: "Walt"),
        Quote(quote: "Yeah, science!", author: "Jesse")
    ]
    
    func getData() async {

        // 1. create the url
        guard let url = URL(string: "https://api.breakingbadquotes.xyz/v1/quotes/5") else {
            print("bad url")
            return
        }
        
        // 2. fetch data from that url
        do {
            let (data, _) = try await URLSession.shared.data(from: url)
            
            // decode that data
            if let decodedData = try? JSONDecoder().decode([Quote].self, from: data) {
                quotes = decodedData
            }
        } catch {
            print("invalid data")
        }
    }
}

struct ContentView: View {
    
    @StateObject private var vm = ContentViewModel()
    
    var body: some View {
        
        NavigationStack {
            
            // ===>>> DID NOT NEED TO ADD A FOREACH INSIDE A LIST!!!
            //List {
                //ForEach(vm.dummyData, id: \.quote_id) { quote in
            
            List(vm.quotes, id: \.quote) { quote in
                VStack(alignment: .leading) {
                    
                    Text(quote.quote)
                        .font(.system(size: 26, weight: .bold))
                        .foregroundColor(Color("skyBlue"))
                    
                    Text(quote.author)
                        .font(.system(size: 22, weight: .bold))
                        .foregroundColor(.gray)
                        .foregroundColor(.secondary)
                }
                .padding()
            }
            .navigationTitle("Quotes")
            .task {
                await vm.getData()
            }
        }
    }
}
```
