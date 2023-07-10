# Audio

[Apple Door](https://developer.apple.com/audio/)

- AVAudioEngine
- AVAudioNode 边播边缓存 installTapOnBus
  - AVAudioMainMixerNode 主混音节点
    - 可以有多个子混音节点 AVAudioMixerNode
    - 可以有多个总线
- AVAudioUnit
  - AVAudioUnitEffect
    - AVAudioUnitEQ
    - 延迟效果 AVAudioUnitDelay
    - AVAudioUnitReverb
    - 时间控制单元: AVAudioUnitTimePitch
    - 失真效果: AVAudioUnitDistorition
- AVAudioFile
- AVAudioFrameCount
- AVAudioFormat
  - sampleRate
  - channelLayout
  - channelCount
  - channelFormat
- AVAudioPlayerNode 播放音频节点

编码
