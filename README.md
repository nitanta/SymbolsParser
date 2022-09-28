# SymbolkitParser

Symbol parser usin symbolkit

##How to access to a framework module Interface (a.k.a. .swiftinterface):
• get the sdk path $ xcrun --show-sdk-path --sdk iphoneos
find the framework you’re interested in, and search the interface there. e.g. for SwiftUI: /System/Library/Frameworks/SwiftUI.framework/Modules/SwiftUI.swiftmodule/arm64-apple-ios.swiftinterface
Docc way to extract info:
Use swift symbolgraph-extract :
$ swift symbolgraph-extract -module-name SwiftUI -v -output-dir . -target arm64-apple-ios -sdk $(xcrun --show-sdk-path --sdk iphoneos)
2. Feed it to SymbolKit (https://github.com/apple/swift-docc-symbolkit.git) and do whathever you want with it!

```
import Foundation
import SymbolKit

@main
struct Symbols {
  /// Currently this script builds a mermaid chart.
  /// Go to https://mermaid-js.github.io/mermaid-live-editor/ and paste
  /// everything that this script prints to see the chart come to life.
  ///
  /// Usage example: $ swift run symbols-cli ../symbols/13.3.1/SwiftUI.symbols.json
  static func main() throws {
    let arguments: [String] = Array(CommandLine.arguments.dropFirst())

    /// The complete `SwiftUI.symbols.json` path (can be relative).
    guard let path: String = arguments.first else { exit(EXIT_FAILURE) }

    let jsonData = try Data(contentsOf: URL(fileURLWithPath: path))
    let symbolGraph = try JSONDecoder().decode(SymbolGraph.self, from: jsonData)
    for relationship in symbolGraph.relationships where relationship.kind == .conformsTo || relationship.kind == .inheritsFrom {
      if let source: SymbolGraph.Symbol = symbolGraph.symbols[relationship.source],
         let target: SymbolGraph.Symbol = symbolGraph.symbols[relationship.target] {

        print("\(source.names.title) --> \(target.names.title)")
      }
    }
  }
}
```
