#
# Perl-Python code base
#

FROM dockermediacloud/base:latest

# Install Python
RUN \
    apt-key adv --keyserver keyserver.ubuntu.com --recv-keys F23C5A6CF475977595C89F51BA6932366A755776 && \
    echo "deb http://ppa.launchpad.net/deadsnakes/ppa/ubuntu xenial main" > /etc/apt/sources.list.d/deadsnakes.list && \
    apt-get update && \
    apt-get -y --no-install-recommends install \
        build-essential \
        python3.7 \
        python3.7-dev \
    && \
    #
    # We don't install Python 3.5 via APT's "python3" package but want to make
    # Python 3.7 be available via "python3" binary
    ln -s /usr/bin/python3.7 /usr/bin/python3 && \
    ln -s /usr/lib/python3.7/ /usr/lib/python3/ && \
    #
    # Install Pip
    curl https://bootstrap.pypa.io/get-pip.py | sudo -H python3.7 && \
    rm -rf /root/.cache/ && \
    true

# CPAN mirror URL (CPAN, DarkPan or MiniCPAN)
ENV MC_PERL_CPAN_MIRROR "https://s3.amazonaws.com/mediacloud-minicpan/minicpan-20170824/"

# Use correct Python with Inline::Python
ENV INLINE_PYTHON_EXECUTABLE /usr/bin/python3.7

# Install Git (for fetching patched Inline::Python) and Perl itself
RUN apt-get -y --no-install-recommends install git perl

# Install CPM (newer rewrite of cpanminus with parallel support)
RUN \
    curl -fsSL --compressed https://raw.githubusercontent.com/skaji/cpm/0.988/cpm > /usr/bin/cpm && \
    chmod +x /usr/bin/cpm && \
    true

# Install patched Inline::Perl
RUN \
    #
    # Install Inline
    cpm install \
        --global \
        --resolver 02packages \
        --no-prebuilt \
        --mirror "$MC_PERL_CPAN_MIRROR" \
        # Explicitly run tests for this code module:
        --test \
        Inline && \
    #
    # Install Inline::Python variant which die()s with tracebacks (stack traces)
    cpm install \
        --global \
        --resolver 02packages \
        --no-prebuilt \
        --mirror "$MC_PERL_CPAN_MIRROR" \
        # Explicitly run tests for this code module:
        --test \
        https://github.com/berkmancenter/mediacloud-inline-python-pm.git@v0.56.2-mediacloud && \
    #
    # Cleanup
    rm -rf /root/.perl-cpm/ && \
    #
    true
