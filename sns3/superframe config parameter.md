# superframe_config_parameter

| 參數                            | 說明                                  | 對應程式                          |
| ----------------------------- | ----------------------------------- | ---------------------------------- |
| `FrameCount`                  | 一個 superframe 包含的 frame 數量（=10）     | `satellite-frame-conf.cc`          |
| `FrameId`                     | frame 編號（0 ~ 9）                     | `satellite-superframe-sequence.cc` |
| `RandomAccessFrame`           | 是否為 RA frame（Frame 1 = true）        | `satellite-frame-conf.cc`          |
| `AllocatedBandwidthHz`        | 該 frame 可用總頻寬                       | `satellite-frame-conf.cc`          |
| `CarrierAllocatedBandwidthHz` | 單一 carrier 的頻寬                      | `satellite-frame-conf.cc`          |
| `CarrierCount`                | 每個 frame 的 carrier 數量               | AllocBW / CarrierBW                |
| `TargetDuration`              | frame / superframe 的時間長度（例如 100 ms） | `satellite-superframe-sequence.cc` |
| `SlotDuration`                | 單一 slot 的時間長度（burst duration）       | `satellite-wave-form-conf.cc`      |
| `SlotsPerCarrier`             | 每個 carrier 的 slot 數量                | TargetDuration / SlotDuration      |
| `GuardTimeSymbols`            | guard time（避免干擾）                    | `satellite-frame-conf.cc`          |
| `LowerLayerService`           | frame 對應的 service / channel id      | `satellite-frame-conf.cc`          |
| `TBTP`                        | 指定 Assigned slots 給哪個 UT            | Scheduler / GW                     |
| `MF-TDMA`                     | Data frame 使用的多頻多時分存取方式             | RTN MAC                            |
