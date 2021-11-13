---
title: "Release Notes"
free: true
---

# v0.6.3

## 新機能
- Visual Studio 2022 に対応しました ([#683](https://github.com/Siv3D/OpenSiv3D/issues/683))
- SimpleGUI にリストボックスを追加しました ([#659](https://github.com/Siv3D/OpenSiv3D/issues/659))
```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	ListBoxState ls1{
		{
			U"北海道", U"青森県", U"岩手県", U"宮城県", U"秋田県", U"山形県", U"福島県", U"茨城県",
			U"栃木県", U"群馬県", U"埼玉県", U"千葉県", U"東京都", U"神奈川県", U"新潟県", U"富山県",
			U"石川県", U"福井県", U"山梨県", U"長野県", U"岐阜県", U"静岡県", U"愛知県", U"三重県",
			U"滋賀県", U"京都府", U"大阪府", U"兵庫県", U"奈良県", U"和歌山県", U"鳥取県", U"島根県",
			U"岡山県", U"広島県", U"山口県", U"徳島県", U"香川県", U"愛媛県", U"高知県", U"福岡県",
			U"佐賀県", U"長崎県", U"熊本県", U"大分県", U"宮崎県", U"鹿児島県", U"沖縄県",
		}
	};

	ListBoxState ls2{
		{
			U"吾輩は猫である（1905年1月 - 1906年8月、『ホトトギス』/1905年10月 - 1907年5月、大倉書店・服部書店）",
			U"坊っちゃん（1906年4月、『ホトトギス』/1907年、春陽堂刊『鶉籠』収録）",
			U"草枕（1906年9月、『新小説』/『鶉籠』収録）",
			U"二百十日（1906年10月、『中央公論』/『鶉籠』収録）",
			U"野分（1907年1月、『ホトトギス』/1908年、春陽堂刊『草合』収録）",
			U"虞美人草（1907年6月 - 10月、『朝日新聞』/1908年1月、春陽堂）",
			U"坑夫（1908年1月 - 4月、『朝日新聞』/『草合』収録）",
			U"三四郎（1908年9 - 12月、『朝日新聞』/1909年5月、春陽堂）",
			U"それから（1909年6 - 10月、『朝日新聞』/1910年1月、春陽堂）",
			U"門（1910年3月 - 6月、『朝日新聞』/1911年1月、春陽堂）",
			U"彼岸過迄（1912年1月 - 4月、『朝日新聞』/1912年9月、春陽堂）",
			U"行人（1912年12月 - 1913年11月、『朝日新聞』/1914年1月、大倉書店）",
			U"こゝろ（1914年4月 - 8月、『朝日新聞』/1914年9月、岩波書店）",
			U"道草（1915年6月 - 9月、『朝日新聞』/1915年10月、岩波書店）",
			U"明暗（1916年5月 - 12月、『朝日新聞』/1917年1月、岩波書店）",
		}
	};

	ls2.selectedItemIndex = 3;

	ListBoxState ls3 = ls2;

	while (System::Update())
	{
		if (SimpleGUI::ListBox(ls1, Vec2{ 20, 20 }, 120, 156) && ls1.selectedItemIndex)
		{

		}

		if (SimpleGUI::ListBox(ls2, Vec2{ 180, 20 }, 240, 156, false) && ls2.selectedItemIndex)
		{

		}

		if (SimpleGUI::ListBox(ls3, Vec2{ 20, 200 }, 1020, 480) && ls3.selectedItemIndex)
		{

		}
	}
}
```
- 同梱する Color Emoji を更新し、Unicode 14.0 の絵文字を扱えるようにしました ([#694](https://github.com/Siv3D/OpenSiv3D/issues/694))
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });

	const Texture e0{ U"🫠"_emoji };
	const Texture e1{ U"🫣"_emoji };
	const Texture e2{ U"🫡"_emoji };
	const Texture e3{ U"🫥"_emoji };
	const Texture e4{ U"🫵"_emoji };
	const Texture e5{ U"🧌"_emoji };
	const Texture e6{ U"🪸"_emoji };
	const Texture e7{ U"🪺"_emoji };
	const Texture e8{ U"🫘"_emoji };
	const Texture e9{ U"🫙"_emoji };
	const Texture e10{ U"🫧"_emoji };
	const Texture e11{ U"🛞"_emoji };

	while (System::Update())
	{
		e0.drawAt(100, 100);
		e1.drawAt(300, 100);
		e2.drawAt(500, 100);
		e3.drawAt(700, 100);
		e4.drawAt(100, 300);
		e5.drawAt(300, 300);
		e6.drawAt(500, 300);
		e7.drawAt(700, 300);
		e8.drawAt(100, 500);
		e9.drawAt(300, 500);
		e10.drawAt(500, 500);
		e11.drawAt(700, 500);
	}
}
```
- GUI フォントに、デフォルトでアイコンフォントへのフォールバックを追加しました。SimpleGUI のテキストで `U"\U000F0493 Setting"` のようにアイコンコードを使ってアイコンを表示できます ([#698](https://github.com/Siv3D/OpenSiv3D/issues/698))
```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
	double volume = 1.0;

	while (System::Update())
	{
		SimpleGUI::Button(U"\U000F1677 ゆっくり", Vec2{ 20, 20 }, 160);
		SimpleGUI::Button(U"\U000F0907 いそいで", Vec2{ 20, 60 }, 160);
		SimpleGUI::Button(U"\U000F0493 設定", Vec2{ 20, 100 }, 160);
		SimpleGUI::Slider(0.5 < volume ? U"\U000F057E"
			: 0.0 < volume ? U"\U000F0580" : U"\U000F0581", volume, Vec2{ 20, 140 }, 30, 130);
	}
}
```
- Windows 版の `System::EnumerateMonitors()` において、より区別しやすいモニターの名前を取得するようにしました ([#695](https://github.com/Siv3D/OpenSiv3D/issues/695))
- 文字を 3D の Mesh で表現するための `MeshGlyph` クラスを追加しました ([#680](https://github.com/Siv3D/OpenSiv3D/issues/680))
```cpp
# include <Siv3D.hpp>

class Font3D
{
public:

	Font3D() = default;

	SIV3D_NODISCARD_CXX20
	explicit Font3D(const Font& font)
		: m_font{ font } {}

	[[nodiscard]]
	Array<MeshGlyph> getGlyphs(StringView s) const
	{
		Array<MeshGlyph> results;

		for (auto ch : s)
		{
			auto it = m_table.find(ch);

			if (it == m_table.end())
			{
				it = m_table.emplace(ch, m_font.createMesh(ch)).first;
			}

			results << it->second;
		}

		return results;
	}

	void drawCylindricalInner(StringView s, const Vec3& center, double r, double scale, double startAngle, const ColorF& color) const
	{
		const double perimeter = (r * Math::TwoPi);
		double penPosX = 0.0;
		startAngle += 90_deg;

		for (auto meshGlyph : getGlyphs(s))
		{
			penPosX += (meshGlyph.xOffset * scale);

			if (meshGlyph.mesh)
			{
				const double angle = (penPosX / perimeter) * 360_deg;
				const Quaternion q = Quaternion::RotateY(-90_deg + angle - startAngle);
				const Vec3 pos = Cylindrical{ r, (-180_deg - angle + startAngle), 0.0 } + center;
				const Mat4x4 mat = Mat4x4::Translate(-meshGlyph.xOffset, 0, 0)
					.scaled(scale)
					.rotated(q)
					.translated(pos);
				meshGlyph.mesh.draw(mat, color);
			}

			penPosX += (meshGlyph.xAdvance - meshGlyph.xOffset) * scale;
		}
	}

	void drawCylindricalOuter(StringView s, const Vec3& center, double r, double scale, double startAngle, const ColorF& color) const
	{
		const double perimeter = (r * Math::TwoPi);
		double penPosX = 0.0;
		startAngle += 90_deg;

		for (auto meshGlyph : getGlyphs(s))
		{
			penPosX += (meshGlyph.xOffset * scale);

			if (meshGlyph.mesh)
			{
				const double angle = (penPosX / perimeter) * 360_deg;
				const Quaternion q = Quaternion::RotateY(90_deg - angle - startAngle);
				const Vec3 pos = Cylindrical{ r, (180_deg + angle + startAngle), 0.0 } + center;
				const Mat4x4 mat = Mat4x4::Translate(-meshGlyph.xOffset, 0, 0)
					.scaled(scale)
					.rotated(q)
					.translated(pos);
				meshGlyph.mesh.draw(mat, color);
			}

			penPosX += (meshGlyph.xAdvance - meshGlyph.xOffset) * scale;
		}
	}

private:

	Font m_font;

	mutable HashTable<char32, MeshGlyph> m_table;
};

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.5, 0.6, 0.6 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };
	const Font3D font3D{ Font{ 60 } };

	while (System::Update())
	{
		const double t = Scene::Time();
		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			Graphics3D::SetGlobalAmbientColor(Graphics3D::DefaultGlobalAmbientColor);
			Graphics3D::SetSunColor(ColorF{ 0.75 });

			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
			Plane{ 64 }.draw(uvChecker);
			Cylinder{ Vec3{0,0,0}, Vec3{0, 16, 0}, 5.6 }.draw(ColorF{ 0.25 }.removeSRGBCurve());

			// 3D Text Circle
			{
				// 両面描画、ライティング無効化
				const ScopedRenderStates3D rasterizer{ RasterizerState::SolidCullNone, BlendState::Additive };
				Graphics3D::SetGlobalAmbientColor(ColorF{ 1.0 });
				Graphics3D::SetSunColor(ColorF{ 0.0 });

				font3D.drawCylindricalOuter(DateTime::Now().format(U"HH:mm:ss"), Vec3{ 0, 11.5, 0 }, 6 * 1.2, 3.0 * 1.2, (t * -25_deg), ColorF{ 1.0, 0.98, 0.9 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(DateTime::Now().format(U"HH:mm:ss"), Vec3{ 0, 11.5, 0 }, 6 * 1.2, 3.0 * 1.2, (t * -25_deg) + 180_deg, ColorF{ 1.0, 0.98, 0.9 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"Monday, September 27, 2021", Vec3{ 0, 10, 0 }, 6 * 1.2, 1.2 * 1.2, (t * -50_deg), ColorF{ 1.0, 0.98, 0.9 }.removeSRGBCurve());

				font3D.drawCylindricalOuter(U"NIKKEI 225 30,248.81 +609.41", Vec3{ 0, 8, 0 }, 6, 1.0, (t * -72_deg), ColorF{ 0.6, 1.0, 0.8 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"HANG SENG 24,192,16 -318.82", Vec3{ 0, 7, 0 }, 6, 1.0, (t * -62_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"SHANGHAI 3,613.07 -29.15", Vec3{ 0, 6, 0 }, 6, 1.0, (t * -58_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"FTSE 7,051.48 -26.87", Vec3{ 0, 5, 0 }, 6, 1.0, (t * -70_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"CAC 6,638.46 -63.52", Vec3{ 0, 4, 0 }, 6, 1.0, (t * -60_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"DAX 15,531.75 -112.22", Vec3{ 0, 3, 0 }, 6, 1.0, (t * -66_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"NASDAQ 15,047.70 -4.54", Vec3{ 0, 2, 0 }, 6, 1.0, (t * -68_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());

				font3D.drawCylindricalOuter(U"NIKKEI 225 30,248.81 +609.41", Vec3{ 0, 8, 0 }, 6, 1.0, (t * -72_deg) + 180_deg, ColorF{ 0.6, 1.0, 0.8 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"HANG SENG 24,192,16 -318.82", Vec3{ 0, 7, 0 }, 6, 1.0, (t * -62_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"SHANGHAI 3,613.07 -29.15", Vec3{ 0, 6, 0 }, 6, 1.0, (t * -58_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"FTSE 7,051.48 -26.87", Vec3{ 0, 5, 0 }, 6, 1.0, (t * -70_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"CAC 6,638.46 -63.52", Vec3{ 0, 4, 0 }, 6, 1.0, (t * -60_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"DAX 15,531.75 -112.22", Vec3{ 0, 3, 0 }, 6, 1.0, (t * -66_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				font3D.drawCylindricalOuter(U"NASDAQ 15,047.70 -4.54", Vec3{ 0, 2, 0 }, 6, 1.0, (t * -68_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
			}
		}

		// 3D シーンを 2D シーンに描画
		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```
- Windows 版において、Leap Motion デバイスをサポートしました ([#677](https://github.com/Siv3D/OpenSiv3D/issues/677))
```cpp
// Ultraleap SDK をインストールし、プロジェクトのプロパティの
// 1. インクルード ディレクトリに
// C:\Program Files\Ultraleap\LeapSDK\include を追加。
// 2. ライブラリ ディレクトリに
// C:\Program Files\Ultraleap\LeapSDK\lib\x64 を追加。
// 3. App フォルダに LeapC.dll をコピー。

# include <Siv3D.hpp>

inline constexpr double HandScale = 0.08;

void DrawSphere(uint32 handID, const Vec3& pos)
{
	Sphere{ (pos * HandScale), (6 * HandScale) }
	.draw(HSV{ handID * 60 }.removeSRGBCurve());
}

void DrawCylinder(const Vec3& from, const Vec3& to)
{
	Cylinder{ (from * HandScale), (to * HandScale), (3 * HandScale) }.draw();
}

void Main()
{
	Window::Resize(1280, 720);
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 0, 32, -32 } };

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	size_t trackingModeIndex = 0;
	bool showInfo = true;
	Leap::Connection leap{ Leap::TrackingMode::Desktop };

	if (not leap)
	{
		throw Error{ U"Leap device not found" };
	}

	while (System::Update())
	{
		leap.update();

		camera.update(2.0);
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			if (trackingModeIndex == 0)
			{
				Plane{ 64 }.draw(uvChecker);

				const double z = 6;

				for (auto x : Range(-2, 2))
				{
					Cylinder{ (x * 6.0), 4, z, 2, 8 }.draw(ColorF{ 0.8 }.removeSRGBCurve());
				}

				for (auto x : Range(-8, 8))
				{
					const Box box{ (x * 2), 10, z, 1.8, 1, 10 };
					bool intersect = false;

					for (const auto& hand : leap.getHands())
					{
						for (auto fingerIndex : step(5))
						{
							for (auto boneIndex : Range(1, 3))
							{
								const Vec3 to = hand.fingerBone(fingerIndex, boneIndex).to;
								const Sphere sphere{ (to * HandScale), (6 * HandScale) };

								if (sphere.intersects(box))
								{
									intersect = true;
									break;
								}
							}

							if (intersect)
							{
								break;
							}
						}

						if (intersect)
						{
							break;
						}
					}

					box.draw(HSV{ (x * 40), (intersect ? 0.8 : 0.15), 1.0 }.removeSRGBCurve());
				}
			}

			for (const auto& hand : leap.getHands())
			{
				const auto handID = hand.id();

				for (auto fingerIndex : step(5))
				{
					for (auto boneIndex : step(4))
					{
						const Vec3 from = hand.fingerBone(fingerIndex, boneIndex).from;
						const Vec3 to = hand.fingerBone(fingerIndex, boneIndex).to;

						if (fingerIndex == 4 && boneIndex == 0)
						{
							DrawSphere(handID, from);
						}

						DrawSphere(handID, to);

						if ((fingerIndex != 0 && fingerIndex != 4) && boneIndex == 0)
						{
							continue;
						}

						DrawCylinder(from, to);
					}
				}

				DrawSphere(handID, hand.palmPosition());
				DrawCylinder(hand.fingerBone(0, 0).from, hand.fingerBone(1, 1).from);
				DrawCylinder(hand.fingerBone(1, 1).from, hand.fingerBone(2, 1).from);
				DrawCylinder(hand.fingerBone(2, 1).from, hand.fingerBone(3, 1).from);
				DrawCylinder(hand.fingerBone(3, 1).from, hand.fingerBone(4, 1).from);
				DrawCylinder(hand.fingerBone(0, 0).from, hand.fingerBone(4, 0).from);
			}
		}

		{
			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}

		if (SimpleGUI::RadioButtons(trackingModeIndex, { U"Desktop", U"Head-mounted", U"Screentop" }, Vec2{ 20,20 }))
		{
			leap.setTrackingMode(static_cast<Leap::TrackingMode>(trackingModeIndex));

			if (trackingModeIndex == 0)
			{
				camera = DebugCamera3D{ renderTexture.size(), 30_deg, Vec3{ 0, 32, -32 } };
			}
			else if (trackingModeIndex == 1)
			{
				camera = DebugCamera3D{ renderTexture.size(), 30_deg, Vec3{ 0, 32, -24 }, Vec3{ 0, 0, 8 } };
			}
			else
			{
				camera = DebugCamera3D{ renderTexture.size(), 30_deg, Vec3{ 0, 32, -56 }, Vec3{ 0, 0, -24 } };
			}
		}

		SimpleGUI::CheckBox(showInfo, U"showInfo", Vec2{ 20, 140 });

		if (showInfo)
		{
			for (const auto& hand : leap.getHands())
			{
				const Vec2 palmPos = camera.worldToScreenPoint(hand.palmPosition() * HandScale).xy();

				String ext;
				for (auto fingerIndex : step(5))
				{
					ext << (hand.isExtended(fingerIndex) ? U'1' : U'0');
				}

				const String state = U"pinchDistance: {:.2f}\ngrabAngle: {:.2f}\npinchStrength: {:.2f}\ngrabStrength: {:.2f}\nfingers:{}"_fmt(
					hand.pinchDistance(), hand.grabAngle(), hand.pinchStrength(), hand.grabStrength(), ext);

				font(hand.isLeftHand() ? U"L" : U"R")
					.draw(TextStyle::Outline(0.15, ColorF{ 0.0 }), 100, Arg::rightCenter = palmPos.movedBy(-20, 0));

				font(state)
					.draw(30, Arg::leftCenter = palmPos, ColorF{ 0.15 });
			}
		}
	}
}
```
- `Math::Tau` や `0.5_tau` など、2π を表す定数 τ に対応しました ([#673](https://github.com/Siv3D/OpenSiv3D/issues/673))
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << Math::Pi;
	Print << Math::Tau;

	Print << Math::PiF;
	Print << Math::TauF;

	Print << 0.5_pi;
	Print << 0.5_tau;

	while (System::Update())
	{

	}
}
```
- 異なる種類どうしの `Optional` の比較演算ができるようになりました ([#670](https://github.com/Siv3D/OpenSiv3D/issues/670))
- `BigFloat` に `.isNan()`, `.isInf()` メンバ関数を追加しました ([#669](https://github.com/Siv3D/OpenSiv3D/issues/669))
- `Array`, `Optional`, `BigInt`, `BigFloat` に三方比較演算子を実装しました ([#658](https://github.com/Siv3D/OpenSiv3D/issues/658))
- `String`, `StringView`, `UUIDValue` に三方比較演算子を実装しました ([#664](https://github.com/Siv3D/OpenSiv3D/issues/664))
- `DrawableText::regionBase()` のオーバーロードを追加しました ([#666](https://github.com/Siv3D/OpenSiv3D/issues/666))
- Windows 版において、リフレッシュレート以上の頻度でキーボード入力を取得できる関数 `Platform::Windows::Keyboard::GetEvents()` を追加しました ([#662](https://github.com/Siv3D/OpenSiv3D/issues/662))
```cpp
# include <Siv3D.hpp>

void Main()
{
	uint64 eventIndex = 0;

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Clear", Vec2{ 680, 20 }))
		{
			ClearPrint();
		}

		for (const auto& event : Platform::Windows::Keyboard::GetEvents())
		{
			if (eventIndex < event.eventIndex)
			{
				Print << event.timeMillisec << U": " << Input { InputDeviceType::Keyboard, event.code }.name() << (event.down ? U" down (event)" : U" up (event)");

				eventIndex = event.eventIndex;
			}
		}

		/*
		for (const auto& key : Keyboard::GetAllInputs())
		{
			if (key.down())
			{
				Print << Time::GetMillisec() << U": " << key.name() << U" down";
			}
			else if (key.up())
			{
				Print << Time::GetMillisec() << U": " << key.name() << U" up";
			}
		}
		*/
	}
}
```


## パフォーマンス向上
- スクリプトエンジンの初期化を遅延させ、スクリプト機能を使わない場合のアプリケーション初期化にかかる時間を短縮しました（数十ミリ秒） ([#657](https://github.com/Siv3D/OpenSiv3D/issues/657))
- GLSL シェーダファイルのライセンス記述を簡素化し、ファイルサイズを少し削減しました ([#687](https://github.com/Siv3D/OpenSiv3D/issues/687))
- `HalfFloat` のメンバ関数を constexpr にしました ([#689](https://github.com/Siv3D/OpenSiv3D/pull/689))

## ユーザビリティ向上
- `NotoEmoji-Regular.ttf` をエンジンリソースに含まなくてもエンジンを初期化できるようにしました ([#684](https://github.com/Siv3D/OpenSiv3D/issues/684))
- `NotoSansCJK-Regular.ttc.zstdcmp` や `NotoSansJP-Regular.otf.zstdcmp` の代替にできる、最低限必要なグリフを格納したフォント `engine/font/min/siv3d-min.woff` を追加しました ([#682](https://github.com/Siv3D/OpenSiv3D/issues/682))
- Windows 版インストーラの対応言語を増やしました ([#671](https://github.com/Siv3D/OpenSiv3D/issues/671))

## 仕様変更
- Web 版で通常と同じメインループが書けるようになったため、`SIV3D_MAINLOOP_BEGIN` を削除しました ([#674](https://github.com/Siv3D/OpenSiv3D/issues/674))
- macOS 版と Linux 版において、ログは `std::cout` ではなく `std::clog` および `std::cerr` に出力するようにしました ([#630](https://github.com/Siv3D/OpenSiv3D/issues/630))
- `engine` および `example` フォルダの更新 ([#686](https://github.com/Siv3D/OpenSiv3D/issues/686))

## 不具合・バグ修正
- `DrawableText::draw(double, Arg:: ...)` や `DrawableText::region(double, Arg ...)` の位置が正しくなかったバグを修正しました ([#665](https://github.com/Siv3D/OpenSiv3D/issues/665))
- Windows 版において`Window::IsToggleFullscreenEnabled()` が常に `false` を返すバグを修正しました ([#699](https://github.com/Siv3D/OpenSiv3D/issues/699))
- `HalfFloat{ 0.0f } == HalfFloat{ -0.0f }` が `false` になるバグを修正しました ([#660](https://github.com/Siv3D/OpenSiv3D/issues/660))
- `CircularBase<float, Oclock>` 使用時に発生する警告を解消しました ([#667](https://github.com/Siv3D/OpenSiv3D/issues/667))


## コントリビューション
### コミッタ―
（敬称略）
- [nokotan](https://github.com/nokotan)
  - **Web 版を更新**
- [tetsurom](https://github.com/tetsurom)
  - `HalfFloat` の実装改善
  - `Optional` の実装改善
  - `BigFloat` の実装改善
  - 各種クラスへの三方比較演算子の実装


# v0.6.2

## パフォーマンス向上
- Windows 版でアプリケーションの起動を高速化しました ([#650](https://github.com/Siv3D/OpenSiv3D/issues/650), [#651](https://github.com/Siv3D/OpenSiv3D/issues/651))
- メモリ / VRAM の消費量を削減しました ([#648](https://github.com/Siv3D/OpenSiv3D/issues/648))

## 不具合・バグ修正
- Windows 版で重い描画処理を行ったときに v0.4.3 よりもフレームレートが低下することがあった問題を修正しました ([#652](https://github.com/Siv3D/OpenSiv3D/issues/652))
- Windows 版、プロジェクトテンプレートの stdafx.h を Header Files フィルタに移動しました ([#653](https://github.com/Siv3D/OpenSiv3D/issues/653))


# v0.6.1

## 新機能
- SDF / MSDF テクスチャ描画時のパラメータを簡単に指定できる `Graphics2D::SetSDFParameters(const TextStyle&)`, `Graphics2D::SetMSDFParameters(const TextStyle&)` を追加しました ([#647](https://github.com/Siv3D/OpenSiv3D/issues/647))

## ユーザビリティ向上
- Windows 版のプロジェクトで発生していたビルド時の IntelliSense の警告を 2 件抑制しました ([#643](https://github.com/Siv3D/OpenSiv3D/issues/643))
- ドキュメンテーションを追加しました

## 仕様変更
- `Monitor` は `MonitorInfo` に名前が変更されました ([#649](https://github.com/Siv3D/OpenSiv3D/issues/649))
- `UUID` は `UUIDValue` に名前が変更されました

## 不具合・バグ修正
- v0.6.0 において、Windows 版でタッチ操作をしたときに左クリックと判定されにくくなっていた不具合を修正しました ([#645](https://github.com/Siv3D/OpenSiv3D/issues/645))
- v0.6.0 において、`<Siv3D/Windows/Windows.hpp>` をインクルードするとコンパイルエラーになっていた問題を修正しました ([#644](https://github.com/Siv3D/OpenSiv3D/issues/644))
- v0.6.0 において、`Platform::Windows::GetHWND()` が実装されていなかった問題を修正しました ([#646](https://github.com/Siv3D/OpenSiv3D/issues/646))
- `MathParser` に空の文字列を渡したときに例外を投げないようにしました ([#641](https://github.com/Siv3D/OpenSiv3D/issues/641))


# v0.6.0

[サンプルコード、画像・動画での紹介](https://zenn.dev/reputeless/scraps/f2e28f40ba44f174d87a)

:::message
非常に多くの更新があるため、現在も執筆中です。
:::

## 新機能

### Featured

- 基本的な 3D 描画に対応しました
- C++20 に対応し、Concepts や Designated initialization, コンストラクタへの `[[nodiscard]]`, 宇宙船演算子、より多くの `constexpr`, 新しい標準機能ライブラリ機能などが活用されています
- 試験的な Web 版の実装を追加しました (詳しくは [OpenSiv3D for Web](https://siv3d.kamenokosoft.com/ja/index))
- Windows で OpenGL バックエンドが利用可能になりました (詳しくは チュートリアル 35)
- ファイルの非同期ダウンロードなどを行う HTTP クライアント機能を追加しました
- デフォルトで HighDPI に対応しました
- SVG ファイルの読み込みに対応しました
- MIDI ファイルの読み込みに対応しました
- 動画をテクスチャとして扱える `VideoTexture` クラスを追加しました
- Windows でペンタブレットの入力（筆圧・傾き）を取得する機能を追加しました
- 2D 描画でカスタム頂点シェーダを利用できるようになりました。3D でも頂点シェーダ、ピクセルシェーダをカスタマイズできます
- すべてのプラットフォームでオーディオのフェードイン・フェードアウト（再生、停止、音量、パン、スピード）をサポートしました
- HPF, LPF, ピッチシフトなどのリアルタイム音声フィルタ機能を追加しました
- 文字の輪郭や Polygon を正確に取得できるようになりました
- `Font` のレンダリング形式に SDF / MSDF を指定できるようになりました
- `Font` の拡大縮小描画、輪郭、シャドウに対応しました
- オーディオファイルのストリーミング再生に対応しました
-


### その他

- `String` 型に対応した、正規表現を扱う機能を追加しました
- 実行ファイルに埋める文字列の難読化をする機能を追加しました
- デマングルを行う関数を追加しました
- Kahan の加算アルゴリズムを行うクラスを追加しました
- 128-bit 整数型を追加しました
- `Stopwatch` や `Timer` がユーザ定義の基準時刻インタフェース `ISteadyClock` を利用することで、複数の `Stopwatch` や `Timer` を一括して一時停止させたり、遅く/早く進行させることが容易になりました
- `TimeProfiler` がより詳細な統計情報を提供するようになりました
- 地理空間データの交換形式である GeoJSON を読み込む機能を追加しました
- 多くの数学定数を追加しました
- `JSONReader`, `JSONWriter` を統合した `JSON` クラスを追加しました
- 簡易的なキーフレームによるアニメーションを行う `SimpleAnimation` クラスを追加しました
- 統計処理を行う関数を追加しました
- 数値に応じたカラーマップを行う機能を追加しました
- ベクトルクラスに多数の便利なメンバ関数を追加しました
- 図形クラスに多数の便利なメンバ関数を追加しました
- `Shape2D` にハート形、両方向矢印、Squircle 形を追加しました 
- `Polygon` に柔軟にテクスチャをマッピングする機能を追加しました
- 長方形詰込みに 90° 回転を許可するオプションを追加しました
- ホモグラフィ変換を行うための機能を追加しました
- 各種乱数関数が乱数エンジンを引数に取れるようになりました
- UUID 生成機能を追加しました
- 環境変数の取得機能を追加しました
- コマンドライン引数の取得機能を追加しました
- モニターの物理サイズなど、詳細な情報を取得できるようになりました
- シリアル通信の詳細なオプションを追加しました
- Klat 方式による音声読み上げ機能を追加しました
- 画像形式 WebP, TIFF の読み込みに対応しました
- 音声形式 Opus, AIFF, FLAC, MIDI, WMA の読み込みに対応しました
- 画像の一部分に画像処理を適用できるようになりました
- GrabCut 機能を追加しました
- QR コード生成機能を改善しました
- `VideoWriter` を改善しました
- サウンドフォントファイルを利用できるようになりました
- マウスカーソルのスタイルを追加しました
- 全てのキー入力を取得する関数を追加しました
- アセット管理における非同期ロードがより便利になりました
- example ファイルを多数追加しました
- ナビメッシュがより簡単に使えるようになりました
- `Spline2D` クラスを追加しました
- 図形の輪郭の一部の取得に対応しました
- 図形の Lerp に対応しました
- GPU だけでの三角形描画に対応しました
- カスタムマウスカーソルに対応しました
- オーディオをグループ化し、グループごとに音量を調整できる機能を追加しました
- Ogg Vorbis のループタグ取得に対応しました
- レーベンシュタイン距離機能を追加しました
- 凹包 (Concave hull) 機能を追加しました
- 柔軟な画像デコーダ、エンコーダクラスを追加されました
- 閉 / 開区間を指定した乱数生成を追加しました
- SIV3D_SET() によるビルド時のエンジン設定機能を追加しました
- Effect の再帰が可能になりました
- CJK フォントを追加し、`Print` が中国語、韓国語の表示に対応しました
- 動画ファイルを読み込む `VideoReader` クラスを追加しました
- 2D 物理演算の機能を追加しました
- Siv3D くんドット絵素材が追加されました
- Siv3D くん .obj 3D モデルファイルが追加されました
- `Image::stamp()` が追加されました
- `Line::drawDoubleHeadedArrow()` で両方向矢印を描けるようになりました
- スクリーンショット保存のショートカットキーをカスタマイズできるようになりました
- スクリプト機能を大幅に改善しました
- 2D 図形の交差判定をより多くの組み合わせに対応しました
- 多くの 3D 形状のクラスを追加しました
- Linux 版の IMEの挙動を改善しました 
- (執筆中）
-
-
-
-
-
- 
- ユーザアドオンの追加機能を追加しました
- その他多数の新機能が追加されています

## パフォーマンス向上
- Windows 版のアプリの起動時間が数百ミリ秒前後短縮しました
- `Heterogeneous lookup` により、文字列リテラルや `StringView` で `HashTable` や `HashSet` のルックアップをする際の実行時性能が向上しました
- ファイルの読み書き、画像ファイルや音声ファイルのロードが高速になりました
- `Parse` / `ParseOpt` / `ParseOr` の速度を改善しました
- (執筆中）
- 
- 
- 
- 

## ユーザビリティ向上
- インライン関数が .hpp ファイルから .ipp ファイルに移され、ヘッダファイルが読みやすくなりました
- Windows 版のプロジェクトがデフォルトでプリコンパイル済みファイルを使用するようになり、ビルドが高速化しました
- (執筆中）
- 
- 


## 仕様変更
- bool 型の関数パラメータの多くが、名前の付いた YesNo 型に置き換えられ、コードの可読性が向上しました
- `Optional` 型が C++ 標準の `std::optional` に近い動作をするよう改善されました
- `HashTable` 型が C++ 標準の `std::unordered_map` に近い動作をするよう改善されました
- `KDTree` がより短い記述で利用可能になりました
- `Concurrenttask` は `AsyncTask` に名前が変更されました
- 子プロセス作成関数は `ChildProcess` クラスに統合されました
- `Unicode::FromWString()` は `Unicode::FromWstring()` に名前が変更されました
- 浮動小数点数型の `U"{}"_fmt(x)` は、有効桁数すべてを表示するようになりました
- `Time::Get～` はシステム起動時間ではなく、アプリケーション起動からの時間を返すようになりました
- `CustomStopwatch` は `VariableSpeedStopwatch` に名前が変更されました
- `FileSystem::SpecialFolderPath()` は `FileSystem::GetFolderPath()` に名前が変更されました
- `FileSystem::UniqueFilePath()` は UUID 機能を使って名前を作るようになりました
- `ByteArray` は `Blob` および `MemoryReader` に置き換えられました
- `CSVData` は `CSV` に名前が変更されました
- `INIData` は `INI` に名前が変更されました
- `JSONReader`, `JSONWriter` は `JSON` に統合されました
- `EasingController` は `EasingAB` に名前が変更されました
- `Sprite` は `Buffer2D` に置き換えられました
- インデックス配列は `TriangleIndex` を使うようになりました
- `MessageBox` の仕様が変わりました
- トースト通知の仕様が変わりました
- 物体検出機能は `CascadeClassifier` に置き換えられました
- `Key` は `Input` になりました
- 絵文字とアイコンデータセットを更新し使える絵文字やアイコンの種類が大幅に増えました
- `Image` の最大サイズを 1 辺 8192px → 16384px に拡張しました
- ConstantBuffer サイズ 16 × N バイト制約が撤廃されました
- 並列実行に関する機能は `SIV3D_CONCURRENT` マクロを定義しなくても使えるようになりました
- High DPI ウィンドウがデフォルトになり、`SIV3D_WINDOWS_HIGH_DPI` マクロは廃止されました
- (執筆中）
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 

## 不具合・バグ修正
- `Array` の並列関数の不具合を修正しました
- `AsyncTask` のプラットフォーム間による差異を解消しました
- Windows の `MakeDragDrop()` の不具合を修正しました
- PPM 画像読み込みの不具合を修正しました
- プラットフォームごとの乱数の再現性の改善しました
- QR コード生成の不具合を修正しました
- (執筆中）
- 
- 
- 
- 
- 
- 


## 注意点
- `Math::SmoothDmap()` の引数順が変更されています。コンパイルエラーで発見できません
- フォントの縦書き機能は一時的に非搭載になりました
- 自然言語処理機能は一時的に非搭載になりました
- `SimpleGUIManager` 機能はキャンセルされました
- `NoiseGenerator ` クラスは一時的に非搭載になりました
- Shift_JIS 形式のテキストファイルはサポートしなくなりました
- シーンのリサイズについて、仕組みが変更されました (チュートリアル 15 参照)
- 絵文字のデザインが変更されました
- 乱数の再現性が v0.4.3 と互換がありません
- 2D 物理演算は cm をデフォルトの単位に変更しました
- `Glyph` 単位での描画の方法が変更されました
- Windows 版は `<Siv3D.hpp>` のプリコンパイル済みを全てのソースファイルで自動でインクルードするようになりました。Main.cpp にある `# include <Siv3D.hpp>` は実質的には無意味です。`# define NO_S3D_USING` が必要な場合はプリコンパイル済みヘッダ作成用ヘッダ `stdafx.h` で行ってください
- `Audio` は `Wave` と互換の形式でデータを保持しなくなりました。`.getWave()` は `.getSamples()` に置き換わりました。`GlobalAudio::BusGetSamples()` も利用できます
- 
- 
- 

## コントリビューション
### コミッタ―
（敬称略）
- [nokotan](https://github.com/nokotan)
  - **Web 版開発を全面的に担当**
- [Ebishu-0309](https://github.com/Ebishu-0309)
  - **`Geometry2D::` に多数の関数を実装**
  - **`Shape2D::Squircle()` の実装**
  - `Bezier2`, `Bezier3` の `.boundingRect()` を実装
  - コードの改善
- [taotao54321](https://github.com/taotao54321)
  - `Grid` の修正
  - コードの改善
- [sthairno](https://github.com/sthairno)
  - **Linux 版の IME 処理改善**
- [itakawa](https://github.com/itakawa) 
  - **Siv3D くん .obj ファイル提供**
- [take-cheeze](https://github.com/take-cheeze)
  - **GitHub Actions を使った CI の整備**
- [Luke256](https://github.com/Luke256)
  - コードの改善
- [YASAI03](https://github.com/YASAI03)
  - **HTTP クライアント機能 `SimpleHTTP` の提案・実装**
- [falrnd](https://github.com/falrnd)
  - `Geometry2D` の交差判定の改善
- [yurkth](https://github.com/yurkth)
  - **`GeoJSON` 関連の機能を提案・実装**
- [ianCK](https://github.com/ianCK)
  - コードの改善
- [lriki](https://github.com/lriki)
  - **Siv3D くんドット絵素材の提供**
- [Ryoga-exe](https://github.com/Ryoga-exe)
  - `Color::gamma()` のバグ修正
- [sivboard](https://github.com/sivboard)
  - スクリプト機能の実装追加とバグ修正
- [agehama](https://github.com/agehama)
  - PPM 画像読み込みのバグ修正
- [kurokoji](https://github.com/kurokoji)
  - **Linux 版 MessageBox の追加**
- [ichi-raven](https://github.com/ichi-raven)
  - コードの改善
- [azaika](https://github.com/azaika)
  - **`JSON` クラスの設計・実装**

### OpenSiv3D Challenge 参加者
- #01 統計関数
  - 白坂, マキア, fal_rnd
- #03 Shape2D::Heart
  - 野菜ジュース, てゃいの
- #04 2D 図形の交差判定
  - Ebishu, fal_rnd, きつねび
- [#05 Squircle](https://zenn.dev/link/comments/ed180236e76181)
  - Ebishu, Ryoga.exe
- [#07 国と都市](https://zenn.dev/link/comments/76224190023391)
  - torin (yurkth)
- [#10 `OutlineGlyph` to `Array<Polygon>`](https://zenn.dev/link/comments/e75e5e80eee914)
  - Ebishu, fal_rnd
