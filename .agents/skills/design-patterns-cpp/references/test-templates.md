# Test Templates

Use these templates to show how a pattern refactor can be verified. Adapt to the repository's existing test framework.

## Fake Adapter Test

```cpp
class FakeNvrAdapter final : public INvrAdapter {
public:
    bool login(const NvrLoginInfo& info) override {
        lastLogin = info;
        loggedIn = true;
        return true;
    }

    bool startPreview(int channel) override {
        previewChannels.push_back(channel);
        return loggedIn;
    }

    bool stopPreview(int) override { return true; }
    bool startPlayback(const PlaybackRequest&) override { return true; }
    bool download(const DownloadRequest&) override { return true; }

    bool loggedIn = false;
    NvrLoginInfo lastLogin;
    std::vector<int> previewChannels;
};
```

Use for service tests that should not load vendor SDKs.

## Strategy Selection Test

```cpp
auto client = CommClientFactory::create(CommProtocol::Tcp, parent);
QVERIFY(client != nullptr);
QCOMPARE(client->metaObject()->className(), "TcpCommClient");
```

Also test unknown protocols, disabled protocols, and configuration parsing.

## Qt Signal Test

```cpp
QSignalSpy spy(client, &ICommClient::dataReceived);

client->send(testPayload);

QVERIFY(spy.wait(1000));
QCOMPARE(spy.count(), 1);
QCOMPARE(spy.takeFirst().at(0).toByteArray(), expectedPayload);
```

Use `QSignalSpy` for async QObject behavior. State timeout and thread assumptions.

## Command Undo/Redo Test

```cpp
AnnotationModel model;
AnnotationItem item{/*...*/};
AddBoxCommand command(model, item);

command.execute();
QCOMPARE(model.count(), 1);

command.undo();
QCOMPARE(model.count(), 0);
```

Also test multi-command sequences and redo invalidation after a new command.

## State Transition Test

```cpp
ConnectionContext context;

QCOMPARE(context.stateName(), QString("Disconnected"));
context.connect();
QCOMPARE(context.stateName(), QString("Connecting"));
context.onConnected();
QCOMPARE(context.stateName(), QString("Connected"));
```

Also test invalid transitions, repeated connect/disconnect calls, and error recovery.

## gtest Skeleton

```cpp
TEST(NvrServiceTest, StartsPreviewAfterLogin) {
    auto adapter = std::make_unique<FakeNvrAdapter>();
    auto* fake = adapter.get();
    NvrService service(std::move(adapter));

    EXPECT_TRUE(service.login(validLogin()));
    EXPECT_TRUE(service.startPreview(1));
    EXPECT_EQ(fake->previewChannels, std::vector<int>{1});
}
```

Prefer fake implementations over mocks unless interaction order is the behavior being tested.
