# SwiftUI

## Animations
* [Advanced SwiftUI Animations – Part 1: Paths](https://swiftui-lab.com/swiftui-animations-part1/) - SwiftUI Lab, 29 August 2019
* [Advanced SwiftUI Animations - Advanced SwiftUI Animations – Part 2: GeometryEffect](https://swiftui-lab.com/swiftui-animations-part2/) - SwiftUI Lab, 2 September 2019

## View
* [The lifecycle and semantics of a SwiftUI view](https://www.swiftbysundell.com/articles/the-lifecycle-and-semantics-of-a-swiftui-view/) - Swift by Sundell, 6 December 2020

## State
* [A guide to SwiftUI’s state management system](https://www.swiftbysundell.com/articles/swiftui-state-management-guide/) - Swift by Sundell, 05 July 2020
* [SwiftUI Architecture: Observable Objects, the Environment and Combine (6/7)](https://www.cometchat.com/tutorials/swiftui-architecture-observable-objects-the-environment-and-combine-6-7)
* [What’s the difference between @ObservedObject, @State, and @EnvironmentObject?](https://www.hackingwithswift.com/quick-start/swiftui/whats-the-difference-between-observedobject-state-and-environmentobject) - Hacking with Swift, 11 February 2020
* [SwiftUI Architecture: Observable Objects, the Environment and Combine (6/7)](https://www.cometchat.com/tutorials/swiftui-architecture-observable-objects-the-environment-and-combine-6-7)

## Optimisation, Performance and Profiling
* [Deep Inside Views, State and Performance in SwiftUI](https://medium.com/swlh/deep-inside-views-state-and-performance-in-swiftui-d23a3a44b79) - 10 September 2020
* [How to use Instruments to profile your SwiftUI code and identify slow layouts](https://www.hackingwithswift.com/quick-start/swiftui/how-to-use-instruments-to-profile-your-swiftui-code-and-identify-slow-layouts) - Hacking with Swift, 10 October 2019
* [Safely Updating the View State](https://swiftui-lab.com/state-changes/) - 21 November 2019

## Techniques
* [Dynamically filtering a SwiftUI List](https://www.hackingwithswift.com/books/ios-swiftui/dynamically-filtering-a-swiftui-list) - Hacking with Swift, 18 April 2020
* [Track keyboard height](https://www.cometchat.com/tutorials/swiftui-architecture-observable-objects-the-environment-and-combine-6-7)
* [Impossible grids](https://swiftui-lab.com/impossible-grids/)

# Navigation
* [How to embed a view in a navigation view](https://www.hackingwithswift.com/quick-start/swiftui/how-to-embed-a-view-in-a-navigation-view) - Hacking with Swift, 9th February 2021
* [The Complete Guide to NavigationView in SwiftUI](https://www.hackingwithswift.com/articles/216/complete-guide-to-navigationview-in-swiftui) - Hacking with Swift, 6 January 2021

## Examples

CPU Monitor animation and safely updating SwiftUI View state
``` swift
// Safely Modifying The View State (SwiftUI)
// https://swiftui-lab.com
// https://swiftui-lab.com/state-changes
import SwiftUI

struct CustomView: View {
    
    var body: some View {
        NavigationView {
            List {
                NavigationLink(destination: ExampleView1(), label: { Text("Example #1 - OutOfControlView") })
                NavigationLink(destination: ExampleView2(), label: { Text("Example #2 - Cardinal Direction") })
                NavigationLink(destination: ExampleView3(), label: { Text("Example #3 - Observable Object") })
            }
        }
    }
}

// MARK: - Example #1
struct ExampleView1: View {
    @State private var showOutOfControlView = false
    
    var body: some View {
        VStack(spacing: 10) {
            CPUWheel().frame(height: 150)

            VStack {
                if showOutOfControlView { OutOfControlView() }
            }.frame(height: 80)

            Button(self.showOutOfControlView ? "Hide": "Show") {
                self.showOutOfControlView.toggle()
            }
        }
    }
}

struct OutOfControlView: View {
    @State private var counter = 0
    
    var body: some View {

        DispatchQueue.main.async {
            self.counter += 1
        }
        
        return Text("Computed Times\n\(counter)").multilineTextAlignment(.center)
    }
}

// MARK: - Example #2
struct ExampleView2: View {
    @State private var flag = false
    @State private var cardinalDirection = ""
    
    var body: some View {
        return VStack(spacing: 30) {
            CPUWheel().frame(height: 150)
            
            Text("\(cardinalDirection)").font(.largeTitle)
            Image(systemName: "location.north")
                .resizable()
                .frame(width: 100, height: 100)
                .foregroundColor(.red)
                .modifier(RotateNeedle(cardinalDirection: self.$cardinalDirection, angle: self.flag ? 0 : 360))
            
            
            Button("Animate") {
                withAnimation(.easeInOut(duration: 3.0)) {
                    self.flag.toggle()
                }
            }
        }
    }
}

struct RotateNeedle: GeometryEffect {
    @Binding var cardinalDirection: String
    
    var angle: Double
    
    var animatableData: Double {
        get { angle }
        set { angle = newValue }
    }
    
    func effectValue(size: CGSize) -> ProjectionTransform {
        DispatchQueue.main.async {
            self.cardinalDirection = self.angleToString(self.angle)
        }
        
        let rotation = CGAffineTransform(rotationAngle: CGFloat(angle * (Double.pi / 180.0)))
        let offset1 = CGAffineTransform(translationX: size.width/2.0, y: size.height/2.0)
        let offset2 = CGAffineTransform(translationX: -size.width/2.0, y: -size.height/2.0)
        return ProjectionTransform(offset2.concatenating(rotation).concatenating(offset1))
    }
    
    func angleToString(_ a: Double) -> String {
        switch a {
        case 315..<405:
            fallthrough
        case 0..<45:
            return "North"
        case 45..<135:
            return "East"
        case 135..<225:
            return "South"
        default:
            return "West"
        }
    }
}

// MARK: - Example #3
class MyObservable: ObservableObject {
    @Published var someValue = "Here's a value!"
}

struct ExampleView3: View {
    var body: some View {
        VStack(spacing: 20) {
            MyView()
            
            MyView()
                .environmentObject(MyObservable())
        }
    }
}
extension EnvironmentObject {
    var safeToUse: Bool {
        return (Mirror(reflecting: self).children.first(where: { $0.label == "_store" })?.value as? ObjectType) != nil
    }
}

struct MyView: View {
    @EnvironmentObject var model: MyObservable

    var body: some View {

        let txt = _model.safeToUse ? model.someValue : "No Value"
        
        return Text(txt)
    }
}

// MARK: - CPU Wheel
struct CPUWheel: View {
    @State private var cpu: Int = 0
    
    var timer = Timer.publish(every: 0.1, on: .current, in: .common).autoconnect()
    
    var body: some View {
        let gradient = AngularGradient(gradient: Gradient(colors: [Color.green, Color.yellow, Color.red]),
                                       center: .center, angle: Angle(degrees: 0))
        
        return Circle()
            .stroke(lineWidth: 3)
            .foregroundColor(.primary)
            .background(Circle().fill(gradient).clipShape(CPUClip(pct: Double(self.cpu))))
            .shadow(radius: 4)
            .overlay(CPULabel(pct: self.cpu))
            .onReceive(timer) { _ in
                withAnimation {
                    self.cpu = Int(CPUWheel.cpuUsage())
                }
            }
    }
    
    struct CPULabel: View {
        let pct: Int
        
        var body: some View {
            VStack {
                Text("\(self.pct) %")
                    .font(.largeTitle)
                    
                Text("CPU")
                    .font(.body)
            }.transaction({ $0.animation = nil })
        }
    }
    
    struct CPUClip: Shape {
        var pct: Double
        
        var animatableData: Double {
            get { pct }
            set { pct = newValue }
        }
        
        func path(in rect: CGRect) -> Path {
            var path = Path()

            let c = CGPoint(x: rect.midX, y: rect.midY)
            path.move(to: c)
            path.addArc(center: c,
                        radius: rect.width/2.0,
                        startAngle: Angle(degrees: 0),
                        endAngle: Angle(degrees: (pct/100.0) * 360), clockwise: false)
            
            path.closeSubpath()
            return path
        }
    }
    
    // Source for cpuUsage(): based on https://stackoverflow.com/a/44134397/7786555
    static func cpuUsage() -> Double {
        var kr: kern_return_t
        var task_info_count: mach_msg_type_number_t

        task_info_count = mach_msg_type_number_t(TASK_INFO_MAX)
        var tinfo = [integer_t](repeating: 0, count: Int(task_info_count))

        kr = task_info(mach_task_self_, task_flavor_t(TASK_BASIC_INFO), &tinfo, &task_info_count)
        if kr != KERN_SUCCESS {
            return -1
        }

        return [thread_act_t]().withUnsafeBufferPointer { bufferPointer in
            var thread_list: thread_act_array_t? = UnsafeMutablePointer(mutating: bufferPointer.baseAddress)
            var thread_count: mach_msg_type_number_t = 0
            defer {
                if let thread_list = thread_list {
                    vm_deallocate(mach_task_self_, vm_address_t(bitPattern: thread_list), vm_size_t(Int(thread_count) * MemoryLayout<thread_t>.stride) )
                }
            }

            kr = task_threads(mach_task_self_, &thread_list, &thread_count)

            if kr != KERN_SUCCESS {
                return -1
            }

            var tot_cpu: Double = 0

            if let thread_list = thread_list {

                for j in 0 ..< Int(thread_count) {
                    var thread_info_count = mach_msg_type_number_t(THREAD_INFO_MAX)
                    var thinfo = [integer_t](repeating: 0, count: Int(thread_info_count))
                    kr = thread_info(thread_list[j], thread_flavor_t(THREAD_BASIC_INFO),
                                     &thinfo, &thread_info_count)
                    if kr != KERN_SUCCESS {
                        return -1
                    }

                    let threadBasicInfo = convertThreadInfoToThreadBasicInfo(thinfo)

                    if threadBasicInfo.flags != TH_FLAGS_IDLE {
                        tot_cpu += (Double(threadBasicInfo.cpu_usage) / Double(TH_USAGE_SCALE)) * 100.0
                    }
                } // for each thread
            }
            
            return tot_cpu

        }
    }

    static func convertThreadInfoToThreadBasicInfo(_ threadInfo: [integer_t]) -> thread_basic_info {
        var result = thread_basic_info()

        result.user_time = time_value_t(seconds: threadInfo[0], microseconds: threadInfo[1])
        result.system_time = time_value_t(seconds: threadInfo[2], microseconds: threadInfo[3])
        result.cpu_usage = threadInfo[4]
        result.policy = threadInfo[5]
        result.run_state = threadInfo[6]
        result.flags = threadInfo[7]
        result.suspend_count = threadInfo[8]
        result.sleep_time = threadInfo[9]

        return result
    }

}
```

Conditional loading based on device, specifically screen size
```swift
 private var screenSize: CGSize {
    #if os(iOS) || os(tvOS)
    return UIScreen.main.bounds.size
    #elseif os(watchOS)
    return WKInterfaceDevice.current().screenBounds.size
    #else
    return NSScreen.main?.frame.size ?? .zero
    #endif
  }
```