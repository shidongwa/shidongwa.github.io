


https://stackoverflow.com/questions/61150/mocking-static-blocks-in-java

@RunWith(PowerMockRunner.class)
@PrepareForTest({X.class, Y.class, Z.class})
@SuppressStaticInitializationFor("some.package.ClassWithStaticInit")