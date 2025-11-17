---
title: Creating Chat Bubbles with curls in React Native (svg)
description: Creating Chat Bubbles with curls in React Native (svg)
publicationDate: 2024-06-24
---

![chat-bubble-meme](public/imgs/blogs/chat-bubble.webp)

Recently, while working on a project, I needed to create a chat feature with a UI similar to iMessage. When I reached the part where I had to implement the curls, I considered three options:
- **One:** Use before and after pseudo element. (Challenge:  pseudo classes and element do not work in react-native, hence option 2)
- **Two:** Use two Views one for the curl and another to overlap it. Something like this
```jsx
<View style={styles.chatBubble}>
    <Text style={styles.text}> Hey whats up </Text>
    <View style={styles.curl}></View>
    <View style={styles.curlOverlap}></View>
</View>
```

- **Three:** Use an Svg. ~~Less work :) yay!~~

Initially, I decided to go with option 2 (using two view) but I didn't get the desired result, so I decided to go with using an Svg since I had a tight timeline.

Now enough with my back story in this article I will guide you on how to create a chat bubble curl using an svg in React native, while utilizing Tailwind instead of the stylesheet.

Before we start, here’s a preview of what we want to achieve:


![Final result](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vaxmx28mjqgt8p7qyo62.png)

**Let's get started**

First, we need to install nativewind to our expo app. Check here for installation guide [Nativewind](https://www.nativewind.dev/quick-starts/expo).

Next we need to install `react-native-svg`:
```
npm i react-native-svg
```

Ok, now that we've got our development environment setup we can start coding.

To use our svg we can import `SvgXml` from `react native svg`. This converts a valid XML string into an SVG. Next, we define our XML string:

```jsx
// ..... other imports .....
import { SvgXml } from 'react-native-svg';

// Xml strings for left and right curl svg
const curlRight = `<svg width="17" height="21" viewBox="0 0 17 21" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M16.8869 20.1846C11.6869 20.9846 6.55352 18.1212 4.88685 16.2879C6.60472 12.1914 -4.00107 2.24186 2.99893 2.24148C4.61754 2.24148 6 -1.9986 11.8869 1.1846C11.9081 2.47144 11.8869 6.92582 11.8869 7.6842C11.8869 18.1842 17.8869 19.5813 16.8869 20.1846Z" fill="#2377F1"/>
</svg>
`

const curlLeft = `<svg width="17" height="21" viewBox="0 0 17 21" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M0.11315 20.1846C5.31315 20.9846 10.4465 18.1212 12.1132 16.2879C10.3953 12.1914 21.0011 2.24186 14.0011 2.24148C12.3825 2.24148 11 -1.9986 5.11315 1.1846C5.09194 2.47144 5.11315 6.92582 5.11315 7.6842C5.11315 18.1842 -0.88685 19.5813 0.11315 20.1846Z" fill="white"/>
</svg>
`
```

Now we can continue with creating our views.
```jsx
// .... previous code ....
export default function HomeScreen() {
    const data  = [
        { content: 'Hello', time: "7:30pm", role: 'system', id: 1 },
        { content: "What's up", time: "7:31pm", role: 'user', id: 2 },
    ];

    return (
        <View className="flex flex-1">
            <ImageBackground source={require(`../../assets/images/bg.png`)} resizeMode="repeat" className="bg-slate-300 absolute -z-10 w-full h-full">
                <View className="flex-1 mt-3 mx-3">
                    <FlatList
                        initialNumToRender={3}
                        data={data}
                        showsVerticalScrollIndicator={false}
                        renderItem={({ item }) => {
                            if (item.role === 'user') {
                                return (
                                    <View key={item.id} className={`relative bg-[#2377F1] self-end p-3 px-6 rounded-3xl mt-2 ${item.id === data.length && 'mb-3'}`}>
                                        <View className="flex-row gap-3">
                                            <Text className="text-base max-w-[73%] text-white">{item.content}</Text>
                                            <Text className="text-sm text-white self-end">{item.time}</Text>
                                        </View>

                                    </View>
                                );
                            } else {
                                return (
                                    <View className={`relative bg-white p-3 pl-6 self-start rounded-3xl mt-2 ${item.id === data.length && 'mb-3'} `}>
                                        <View className="flex-row gap-3">
                                            <Text className="text-base max-w-[73%] flex-row">{item.content}</Text>
                                            <Text className="text-sm self-end">{item.time}</Text>
                                        </View>

                                    </View>
                                );
                            }
                        }}
                        keyExtractor={(item) => item.id.toString()}
                        className="mb-14"
                    />
                </View>
            </ImageBackground>
        </View>
    );
}

```

At this stage, we still have a rounded view containing the message because we haven't added the SVG to our views. To add the SVG, we need to pass our XML string to the `SvgXml` tag, like this:
``` jsx
renderItem={({ item }) => {
    if (item.role === 'user') {
        return (
            <View key={item.id} className={`relative bg-[#2377F1] self-end p-3 px-6 rounded-3xl mt-2 ${item.id === data.length && 'mb-3'}`}>
                <View className="flex-row gap-3">
                    <Text className="text-base max-w-[73%] text-white">{item.content}</Text>
                    <Text className="text-sm text-white self-end">{item.time}</Text>
                </View>
                {/* Here for right curl */}
                <SvgXml xml={curlRight} width={20} height={20} className="absolute bottom-0 right-0 z-50" />
            </View>
        );
    } else {
        return (
            <View className={`relative bg-white p-3 pl-6 self-start rounded-3xl mt-2 ${item.id === data.length && 'mb-3'} `}>
                <View className="flex-row gap-3">
                    <Text className="text-base max-w-[73%] flex-row">{item.content}</Text>
                    <Text className="text-sm self-end">{item.time}</Text>
                </View>
                {/* Here for left curl */}
                <SvgXml xml={curlLeft} width={20} height={20} className="absolute bottom-0 left-[-.1rem] z-50" />
            </View>
        );
    }
}}

```

By following these steps, you should now have chat bubbles with curls implemented in your React Native app using SVGs.

**Conclusion**

This approach not only simplifies the code but also enhances the visual appeal of your chat interface. Using SVGs provides greater flexibility and ensures that the curls look sharp on all screen sizes. You can check [Chatbot-rn](https://github.com/Lftobs/Chatbot-rn) if you would like to build a chatbot with Gemini.
