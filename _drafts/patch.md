git diff --no-prefix Gemfile* config/initializers > tmp/use-this-lovely-gem.patch

for i in tmp/*.patch; do patch -p0 < $i; done
for i in tmp/*.patch; do patch -p0 -R -i $i; done
